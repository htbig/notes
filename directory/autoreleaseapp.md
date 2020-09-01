### 安卓版本自动化发布软件

#### 命令
```
/usr/bin/gitlab-ci-multi-runner run --working-directory /home/gitlab-runner --config /etc/gitlab-runner/config.toml --service gitlab-runner --syslog --user gitlab-runner
```
#### 例子
```
下载工程到本地目录:/home/ht/packages下:
sudo git clone xxx.git
cd xxx
sudo cp ../yyy/build.py ./
sudo cp ../yyy/release.py ./
sudo cp ../yyy/.gitlab-ci.yml ./      (需要修改成对应的项目名称)
sudo cp ../yyy/make.sh ./
sudo cp ../yyy/local.properties ./
sudo cp ../yyy/xxxai.keystore ./
在git界面setting里设置ci/di的runnners
在/home/ht/auto.sh里添加xxx的相关选项:
diff --git a/auto.sh b/auto.sh
index b72274b..eeb2537 100755
--- a/auto.sh
+++ b/auto.sh
@@ -52,6 +52,7 @@ _usage ()
     
+    echo "                              xxx"
     echo "    -r <pkg_name_gen>    Set build package genenate name"
     echo "    -m <repo path>    Set pacman repo path, default is /srv/http/testing"
     echo "    -n <repo name>    Set pacman repo name, default is testing"

+if [ ${pkg_name} == "xx" ]; then
+    src_path=/home/ht/packages/xxx
+    root_path=/home/ht/packages/xxx
+fi

每次修改工程app/src下的文件提交代码就会编译出临时的apk，如果修改app/build.gradle文件就会编译出release版本（修改里面的versionName字段）
```
### auto.sh
```
#!/usr/bin/env bash
#
# Auto Builds

#set -e
#set -x

# Colors
color_escape="\x1b["
color_reset=${color_escape}"39;49;00m"
red=${color_escape}"31;01m"
green=${color_escape}"32;01m"
yellow=${color_escape}"33;01m"
blue=${color_escape}"34;01m"
magenta=${color_escape}"35;01m"
cyan=${color_escape}"36;01m"

# Color texts
error_str="${red}Error${color_reset}"
info_str="${green}Info${color_reset}"

step_str="${green}Step${color_reset}"

pkg_name='xxx'
pkg_name_gen='xxx'
pkg_repo_path='/var/www/html/debs'
pkg_repo_name='debs'
force_build=0
need_release=0

_usage ()
{
    echo "usage ${0} [options]"
    echo
    echo "General options:"
    echo "    -p <pkg_name>    Set build package"
    echo "                           build package:"
    echo "				apps"
    echo "    -r <pkg_name_gen>    Set build package genenate name"
    echo "    -m <repo path>    Set pacman repo path, default is /srv/http/testing"
    echo "    -n <repo name>    Set pacman repo name, default is testing"
    echo "    -v                 Enable verbose output"
    echo "    -f                 Force build"
    echo "    -l                 Release app"
    echo "    -h                 This help message"
    exit ${1}
}

while getopts 'p:r:m:n:vflh' arg; do
  case "${arg}" in
    p) pkg_name="${OPTARG}"
        pkg_name_gen="${OPTARG}" ;;
    r) pkg_name_gen="${OPTARG}" ;;
    m) pkg_repo_path="${OPTARG}" ;;
    n) pkg_repo_name="${OPTARG}" ;;
    v) verbose="-v" ;;
    f) force_build=1 ;;
    l) need_release=1 ;;
    h) _usage 0 ;;
      *)
        echo "Invalid argument '${arg}'"
        _usage 1
        ;;
  esac
done

step=1

cur_date=`date "+%F %R"`
root_path=/home/ht/packages/${pkg_name}
src_path=/home/ht/packages/${pkg_name}
if [ ${pkg_name} == "apps" ]; then
    src_path=/home/ht/packages/Apps-Android
    root_path=/home/ht/packages/Apps-Android
fi
echo -e "$step_str $step: begin $cur_date ..."
pkgbuild_file=${src_path}/app/build.gradle
pkgversion_file=${root_path}/VERSION
step=`expr $step + 1`
echo -e "$step_str $step: begin git pull ..."
cd $src_path
if [ ${need_release} == 1 ]; then
    echo "release version"
    `git checkout app/build.gradle`
fi
ret=`git pull`
if [ "${ret}" == "Already up-to-date." ]; then
    echo "Already up-to-date. do nothing!"
    if [ ${force_build} == 0 ]; then
        exit
    fi
fi
echo ${ret} | grep "error"
if [ $? == 0 ]; then
    echo "git pull error, please check it!" | mutt -s "Vega build error" xxx.com
fi
gittag=`git rev-parse --short HEAD`
author=`git log -n 1 --pretty=format:"%an"`
author_email=`git log -n 1 --pretty=format:"%ae"`
message=`git log -n 1 --pretty=format:"%s"`
commit=`git log -n 1 --pretty="%H"`

message=${message//['(',')','#','&','/','\','{','}','[',']']/}

step=`expr $step + 1`
echo -e "$step_str $step: begin build ${pkg_name} package ..."
cur_pkgver=`sed -ne 's/versionName \([0-9]*\)/\1/p' ${pkgbuild_file}`
echo $cur_pkgver
cur_pkgver=`echo $cur_pkgver | sed 's/"//g'`
cur_pkgrel=`sed -ne 's/versionCode \([0-9]*\)/\1/p' ${pkgbuild_file}`
#cur_pkgrel=`expr $cur_pkgrel + 1`
echo "build pkgver: $cur_pkgver pkgrel: $cur_pkgrel ..."
#sed -i -e 's/versionCode \([0-9]*\)/versionCode '"${cur_pkgrel}"'/g' ${pkgbuild_file}
cur_pkgrel=0
package_ver=${cur_pkgver}-${cur_pkgrel}
echo $package_ver
cmd=`sudo ./make.sh`
pkg_filename="new.apk" #"${pkg_name_gen}_${package_ver}_armhf.deb"
if [ -f "${src_path}/${pkg_filename}" ]; then
    echo -e "${info_str}: build success"
    step=`expr $step + 1`
    echo -e "$step_str $step: repo add package: $pkg_filename ..."
    echo $src_path/$pkg_filename
    cp $src_path/$pkg_filename /var/www/html/debs/for_android/${pkg_name}/${pkg_name}_${cur_pkgver}-${cur_pkgrel}-${gittag}.apk
    ln -s -f /var/www/html/debs/for_android/${pkg_name}/${pkg_name}_${cur_pkgver}-${cur_pkgrel}-${gittag}.apk /var/www/html/debs/for_android/$pkg_name/$pkg_name.apk
    if [ ${need_release} == 1 ]; then
	mv $src_path/$pkg_filename /var/www/html/debs/for_android/${pkg_name}/${pkg_name}-${cur_pkgver}-release.apk
       	echo "\"${cur_pkgver}\"" >/var/www/html/debs/for_android/${pkg_name}/version
	su - ht -c "scp /var/www/html/debs/for_android/${pkg_name}/${pkg_name}-$cur_pkgver-release.apk user@ip:/srv/http/debs/for_android/${pkg_name}/"
	su - ht -c "scp /var/www/html/debs/for_android/${pkg_name}/version user@ip:/srv/http/debs/for_android/${pkg_name}/"
    fi
    state="Success"
    state_color="green"
else
    echo -e "${error_str}: build error!"
    state="Error"
    state_color="red"
    echo "${author} ${author_email} build error!" | mutt -s "Vega build error" xx.com
fi

step=`expr $step + 1`
echo -e "$step_str $step: $cur_date autogen ${pkg_name} successfully"

exit 0
```
### .gitlab.yaml
```
# 定义 stages
stages:
    - build
build:
    stage: build
    tags:
        - build-runner-xxx 
    script:
        - need_build=`python build.py`
        - need_release=`python release.py`
        - if [ "$need_release" = "True" ]; then /home/ht/auto.sh -lp apps; elif [ "$need_build" = "True" ]; then /home/ht/auto.sh -p apps; fi
    only:
        - master

```
### build.py
```
# coding: utf-8
#!/bin/python
import os

need_build_dir=['app/src']
need_build_files=[]

def need_build():
    for file in need_build_files:
        output = os.popen('git show %s'%file)
        show_content = output.read()
        show_content_len = len(show_content)
        if show_content_len > 0:
            return True
        continue
    for dir in need_build_dir: 
        output = os.popen('git show %s'%dir)
        show_content = output.read()
        show_content_len = len(show_content)
        if show_content_len > 0:
            return True
        continue
    return False

if __name__ == "__main__":
    if need_build():
        print("True")
    else:
        print("False")


```
### release.py
```
# coding: utf-8
#!/bin/python
import os

need_build_files=['app/build.gradle']
def need_release():
    for file in need_build_files:
        output = os.popen('git show %s'%file)
        show_content = output.read()
        show_content_len = len(show_content)
        if show_content_len > 0:
            return True
    return False

if __name__ == "__main__":
    if need_release():
        print("True")
    else:
        print("False")

```
### 版本号规则
```
主版本号（1）：当功能模块有较大的变动，比如增加多个模块或者整体架构发生变化。此版本号由项目决定是否修改。（另一种说法：产品的主体构件或构件之间的接口协议发生重大变动）

子版本号（0）：当功能点有一定的增加或变化，比如增加了对权限控制、增加自定义视图等功能。此版本号由项目决定是否修改。（另一种说法：产品的数据结构发生变动）

阶段版本号（0）：一般是 Bug 修复或是一些小的变动，要经常发布修订版，时间间隔不限，修复一个严重的bug即可发布一个修订版。此版本号由项目经理决定是否修改。（另一种说法：产品的数据结构未发生变动，仅修复bug）
```
