# Think Space

hello everyone, i'm ht

# 2019 Mar
- [安卓编译](#android_compile)
- [安卓开发](#android_develop)
- [数据库操作](#sql_op)
- [ssh反向隧道](#ssh)
- [git修改远端origin](#git_origin)
- [docker编译](#docker_compile)
- [gitlab-ci](#gitlab-ci)
- [syslog安装](#syslog-ng)
- [集群分布式](# 集群分布式)

# android_compile

#### 第三方apk进系统
* 在packages/apps目录下新建Prebuilt_apps目录，将需要安装的apk文件放到该目录，并且在该目录新建Android.mk文件，内容
```
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_POST_PROCESS_COMMAND := $(shell cp -r $(LOCAL_PATH)/*.apk $(TARGET_OUT)/app/)
```

* 执行make -j8，会将Prebuild_apps的文件拷贝至out/target/product/roc_rk3328_cc_box/system/app,如果apk文件里包含so文件，解压apk，如其中lib(.so)文件，把 .so文件copy到system\lib下， 再编译，这样第三放apk就编译进system.img里了，会在/system/app目录下
* 将/system/app/Prebuilt_apps的文件拷贝到/data/app，这样上电起来，会安装data/app下的apk。
* 可以在/system/bin下新建run_apk.sh脚本，内容
```
  busybox cp /system/app/xxx.apk data/app
  chmod 777 data/app/xxx.apk
```

* 在system/core/rootdir/init.rc添加内容
```
service run_apk /system/bin/run_apk.sh 
class main 
oneshot
```
这样系统起来就会执行run_apk.sh并且安装第三方apk了

#### 让服务上电起来自动开启
* 添加start_app相关的东西用来上电自动启动服务，在device/rockchip/common/sepolicy/file_contexts加入
```
/system/bin/commontools            u:object_r:commontools_exec:s0
wfx@OptiPlex-9020:~/proj/roc-rk3328-cc$ git diff device/rockchip/common/sepolicy/file_contexts
diff --git a/device/rockchip/common/sepolicy/file_contexts b/device/rockchip/common/sepolicy/file_contexts
index a2834af..b078a7b 100755
--- a/device/rockchip/common/sepolicy/file_contexts
+++ b/device/rockchip/common/sepolicy/file_contexts
@@ -138,6 +138,7 @@
 /data/misc/ppp(/.*)?             u:object_r:pppoe_data_file:s0

 /system/bin/wfd                  u:object_r:wfd_exec:s0
+/system/bin/commontools            u:object_r:commontools_exec:s0

 /dev/vendor_storage              u:object_r:storage_device:s0
 
```
* 添加device/rockchip/common/sepolicy/commontools.te文件，其内容
```
wfx@OptiPlex-9020:~/proj/roc-rk3328-cc$ cat device/rockchip/common/sepolicy/commontools.te
type commontools, domain;
type commontools_exec, exec_type, file_type;
init_daemon_domain(commontools)
```
* 添加out/target/product/roc_rk3328_cc_box/system/bin/commontools文件，内容
```
wfx@OptiPlex-9020:~/proj/roc-rk3328-cc$ cat out/target/product/roc_rk3328_cc_box/system/bin/commontools
#! /system/bin/sh
sleep 70
/system/bin/apps &
am start -n com.example.hz08692.aliiotkit/com.example.hz08692.aliiotkit.MainActivity
sleep 1
am start -n com.example.cc.door/com.example.cc.door.MainActivity
sleep 1
am start -n com.example.fjl.rfidservice/com.example.fjl.rfidservice.MainActivity
sleep 1
am start -n com.example.cc.apps/com.example.cc.apps.MainActivity
while true; do  sleep 100; done
```
* 修改system/core/rootdir/init.rc
```
git diff system/core/rootdir/init.rc
+service start_app /system/bin/start_app.sh
+    class main
+    user root
+    group root
```
# android_develop
```
adb root
adb connect 172.21.30.197
adb install apk的路径 就可以直接安装成功
adb uninstall 包名
adb push  C:\\heting\\springboot\\.mvn\\wrapper\\maven-wrapper.jar /data
adb pull /data/user/0/com.example.cc.alarm/databases/alarm.db C:\heting\software\adb

adb shell
pm install app-debug.apk(在安卓系统下执行)
pm uninstall com.example.cc.alarm
pm list package 列出当前设备上已经安装的所有包
pm list package -3 获取安装的第三方库软件
获取所有apk安装路径和对应的包名命令：pm list package -f
获取包名对应的apk了路径命令：pm path 包名，直接返回包的路径
清除apk关联数据命令：pm clear 包名
pm dump com.example.cc.alarm |grep versionName 查看apk版本
pm dump 包名 返回值里有一个stopped=true表示服务停止，stopped=false表示服务在运行
pm install -r app-debug.apk 替换已经安装的apk

交互界面：可以打开文件跳出安装界面问你是否安装
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.setDataAndType(Uri.fromFile(new File("/data/app-debug.apk")),
        "application/vnd.android.package-archive");
//startActivity(intent);
startActivity(Intent.createChooser(intent, "Apps"));
```
* 我们知道AOSP项目由不同的子项目组成,为了方便进行管理,Google采用Git对AOSP项目进行多仓库管理，repo就是这么一种工具,由一系列python脚本组成,通过调用Git命令实现对AOSP项目的管理
编译安卓系统：https://www.linuxidc.com/Linux/2017-09/147107.htm
https://blog.csdn.net/lyb2518/article/details/77072792（更详细）
编译安卓系统花了2：45，两小时45分
* 在安卓源码下重新打包apk包，java -Djava.library.path=/home/ht/aosp/prebuilts/sdk/tools/linux/lib64  -jar  signapk.jar platform.x509.pem platform.pk8 app-debug.apk  app-debugnew.apk

* 重新挂载分区
```
mount -o rw,remount /system  重新mount/system为可读写
```
* 在apk安装时，系统默认会给每个app分配一个uid，在/data/system/packages.xml文件中可以查看到所有安装的app的uid。在默认情况下每个app有自己的uid，只能够访问自己的数据，如果多个app设置了相同的uid，他们就能运行在同一个进程中，就能够实现数据的共享。当程序想要获取系统权限时，将android:SharedUserId 属性设置为”android.uid.system"，可以让程序运行在系统进程中，能够实现系统时间的修改。 但是只是设置sharedUserId并不能够实现去获取系统权限，想要获取系统权限还必须要有相应的签名。
android:sharedUserId="android.uid.system"添加在<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.cc.apps" >加系统权限
要获得系统权限，在完成程序的编写后，在配置文件AndroidManifest.xml中加入android:sharedUserId="android.uid.system，如下文
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.date"
    android:versionCode="1"
    android:versionName="1.0" 
    android:sharedUserId="android.uid.system">
```
加入这句命令，告诉系统需要权限，但并没有给系统权限，下面两种是大家用到的比较多的办法：
第一种：
```
需要在linux环境下完成，而且是编译android源码的环境下，修改Android.mk文件，加入LOCAL_CERTIFICATE := platform这一行，使用mm命令来编译，生成的apk就有修改系统时间的权限了（这个我没有操作）。
```
第二种
```
不需要在源码环境下，但还是要求你有android的源码，而且我第一次弄的时候还是跑到linux底下了。

使用eclipse编译出apk文件。使用目标系统的platform密钥来重新给apk文件签名。首先找到密钥文件，在我

android源码目录中的位置是"build/target/product/security"，

下面的platform.pk8和platform.x509.pem两个文件。然后用Android提供的Signapk工具来签名，signapk的源代码是在

"build/tools/signapk"下，

编译后在out/host/linux-x86/framework下，用法为

java -jar signapk.jar platform.x509.pem platform.pk8 input.apk output.apk"。
sudo java -Djava.library.path=/home/ht/rk3328/prebuilts/sdk/tools/linux/lib64  -jar  signapk.jar platform.x509.pem platform.pk8 app-debug.apk  app-debugnew.apk
这里注意一下，好多人都直接说编译，让小白很难受啊，我说详细点，就是在linux环境下，去到"build/target/product/security"，是一个.java文件，我用的javac编译通过，但也提示了一堆东西没有"javac SignApk.java"。我用mm来编译，死活不成功，提示/usr/bin/mm没有那个文件或目录。

这里再说一下，因为这里是对apk进行操作的，所以不用IDE的debug来调试，如果没有提前在path里面设置环境变量的话，可以到android的sdk目录platform-tools里有adb，用cmd命令窗来定位，然后执行adb install  yourapkname.apk。如果不是第一次安装，建议把之前的卸载，再安装
```
* 设置adbd运行端口：setprop service.adb.tcp.port 5555 
/sbin/adbd重新运行adbd
* 交互式的安装apk
```
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.setDataAndType(Uri.fromFile(new File("/data/app-debug.apk")),
        "application/vnd.android.package-archive");
startActivity(intent);
startActivity(Intent.createChooser(intent, "Apps"));
```
* 安卓下直接停止服务命令：stop alarm等,alarm是写在了init.rc文件
```
am start -n com.android.chrome/com.google.android.apps.chrome.Main启动chrome
am start org.xbmc.kodi/org.xbmc.kodi.Main 启动kodi播放器
am start -n com.android.chrome/com.google.android.apps.chrome.Main -d www.baidu.com起来打开百度
```

* 安卓tv和普通安卓
```
3328只有box的，另一种是 mid，3288,3399,3368都有mid
mid一般用于平板。但像3288,3399,3368这些也要有SDK编译，SDK需要Licence，一般我们不提供固件。
```
* 移植IGSS到安卓系
```
1:修改目录/vbg/---->/data/vbg
2:panic: sql: unknown driver "sqlite3" (forgotten import?),这个是因为sqlite3是cgo，需要CGO_ENABLED=1,打开此选项之后就会有一些动态库需要包含，所以-ldflags '-linkmode "external" -extldflags "-static"'编译成静态的。
3:"/bin/sh"没有换成"/system/bin/sh"
4:"python3和python2"没有,安装qpython在安卓系统上
qpython3.2不支持requests包所以得安装https://github.com/qpython-android/qpy36/releases，3.6的qpython，接着跑gobox-tools，继续安装flask，flask_apscheduler
python3 ./pip3 install flask_apscheduler
pytz: timezone,需要/etc/localtime配置文件，但是unix.py系统文件里写的就是/etc/localtime但是安卓系统上文件没有，所以我修改了unix.py文件可以通过了
接着是ping_job，会fork失败，/data/user/0/org.qpython.qpy3/files/lib/python36.zip/subprocess.py文件FileNotFoundError: [Errno 2] No such file or directory: '/bin/sh': '/bin/sh'出错，估计也得修改源码,修改了subprocess.py，可以了
```
* 编译安卓uboot,kernel,sdk：
```
repo init --repo-url ssh://git@www.rockchip.com.cn/repo/rk/tools/repo -u ssh://git@www.rockchip.com.cn/gerrit/rk/platform/manifest -b android-7.0 -m rk3328_box_nougat_release.xml
中间碰到代码拉不下来，执行了.repo/repo/repo sync --force-sync
https://blog.csdn.net/kc58236582/article/details/72468770安卓预编译服务器
1：Communication error with Jack server (58), try 'jack-diagnose' or see Jack server log
Android7.0 JACK编译器不支持多用户同时编译，其他用户在编译，端口被占用解决方法：
如下：修改$HOME/.jack-settings和$HOME/.jack-server/config.properties中的端口号（比如都改为8096/8097），方可支持多用户同时编译。
编译uboot和kernel要分别进入两个目录去编译
uboot:make rk3328_box_defconfig make ARCHV=aarch64 -j12
kernel:make ARCH=arm64 rockchip_smp_nougat_defconfig make ARCH=arm64 rk3328-box.img
编译sdk：
source javaenv.sh
source build/envsetup.sh
lunch
make -j8
编译ko:
make ARCH=arm64 drivers/net/wireless/rockchip_wlan/rkwifi/bcmdhd/bcmdhd.ko
将image都放进rockdev目录下
./mkimage.sh
rk固件:
./FFTools/mkupdate/sd_mkupdate.sh update可以打包固件，最终生成的文件是 rockdev/Image-rk3328_firefly_box/update.img
system/core/rootdir/init.box.rc下是init.rc文件
```
* 编译特定模块方法：
```
1、. build/envsetup.sh 
2、mmm hardware/libhardware_legacy/power/ 

或者 ： 

1、. build/envsetup.sh 
2、cd hardware/libhardware_legacy/power/ 
3、mm 
```

* busybox wget http://tx.ustarcloud.com:20070/apps/version
```
tx.ustarcloud.com ip为：106.12.220.233
```
* 将制作的tf卡内容dd到image文件，然后在dd回其他tf卡
```
dd if=/dev/sdc of=/home/ht/image/image_16
dd if=/home/ht/image/image_16 of=/dev/sdc(速度5M，6M多)
sudo watch -n 5 pkill -USR1 ^dd查看dd 进度
```
# sql_op
* 编写django代码有创建数据库到生成models.py文件步骤
```
1：在/home/ht/iot_cloud/scripts/database/cloud_db.sql文件中添加表，修改了数据库
2： ./manage.py inspectdb --settings dc.settings.develop生成models.py，将刚才添加的表的对应model定义内容写到django对应app的models.py文件里去
```
* select count(*) from p_cloud_alm_info;
* select count(*) from p_cloud_alm_info where (alm_id=205);
* delete from p_cloud_alm_info where report_time >='2018-04-21' and report_time < '2018-05-1';
* update p_dev_info set phy_state=0 where dev_id != 2004 and dev_id != 2020;
* delete from p_sw_version where release_ver='1.0.6.0-GA';
# ssh
```
ssh反向隧道同一局域网的特定端口：
ssh -gNfL 9999:192.168.166.68:80 pi@127.0.0.1    //将同局域网内的192.168.166.68的80端口映射到本机9999端口
ssh -NfR 6670:localhost:9999 ici@m.vegcloud.tech  //将本机的9999端口映射到公网的6670端口。
-C：该参数将使ssh压缩所有通过Secure Shell客户端发送的数据，包括输入、输出、错误消息及转发数据。它使用gzip算法，压缩级别可通过设置配制文件中的参数Compressicn Level来指定。这对于缓慢的传输线路特别有用的。但对于传输速度已经很快的网络则显得没有必要。同样，你可以利用配制文件针对每台主机配置这个参数。 
-f：该参数将ssh连接送入后台执行。这在验证已经完成且TCP/IP转发已经建立的情况下会生效。这对在远程主机上启动X程序显得十分重要。其后用户将被提示要求输入口令(提供的认证代理不运行)，然后将连接送往后台。 
-g：该参数允许远程主机通过端口转发与主机端口相连，通常情况下仅允许本地主机这样做。 
-N：不执行远程指令。 
-R：远程转发 
-L：本地转发
如果出现ERR_UNSAFE_PORT，要么改变需要代理的端口，要么修改浏览器属性，起浏览器的时候添加参数--explicitly-allowed-ports=87,6666,556,6667，其中后面逗号隔开的是允许的端口
```
# git_origin
```
git remote rm origin
git remote add origin http://192.168.100.235:9797/john/git_test.git
```
# docker_compile
```
docker编译记录：
编译都是在docker提供的container里进行，并且docker server和docker cli分开变成了俩个projects
分别为：
https://github.com/docker/docker.git
 https://github.com/docker/cli
遇到部署说却libapparmor问题直接进编译环境的container，将环境里的libapparmor.so文件拷贝出来，放到运行编译出来的docker的宿主机上。
编译cli是用make -f  dockerfile/Dockerfile.dev binary
```
# gitlab-ci
* 安装gitlab
* 安装gitlab-runner
```
Running with gitlab-ci-multi-runner dev (HEAD)
  on gitlab runner for build server (2d60be39)
Using Shell executor...
Running on archiso...
Fetching changes...
HEAD is now at 70ebf78 Update build.py
From http://172.21.148.250:10080/veg/vega_api_video
   70ebf78..ba0be55  master     -> origin/master
Checking out ba0be55f as master...
Skipping Git submodules setup
$ need_build_iot=`python build.py`
$ echo $need_build_iot
/home/admin/gitlab-runner/builds/2d60be39/0/veg/vega_api_video False
$ if [ "$need_build_iot" = "True" ]; then /home/admin/veg_auto_build.sh -p vegavideo; fi
Job succeeded
gitlab ci gitlab已经自带，不用再安装，.gitlab-ci.yml就是由它解析
gitlab runner需要自己安装到一台服务器上，它是脚本执行的载体，gitlab-ci根据gitlab-ci.yml文件解析出来的runner，分配到对应的runner服务器上去跑
.gitlab-ci.yml文件是项目根目录的一个文件，ci在收到push操作之后会解析这个文件然后安排runner
安装完git runner之后要向 gitlab ci注册我的runner，不然push事件来的时候不知道调用谁执行呢？
runner就是跑gitlab-ci需要执行脚本，在安装了gitlab runner的机器上
```
# syslog-ng
```
syslog服务搭建在arch上：
pacman -S syslog-ng
```
# 集群分布式
```
集群分布式
首先我们先了解一下分布式和集群的由来。在开始的时候，网站都是一个简单的架构，例如LAMP的架构，就在一台服务器上部署了各种应用程序，访问的人少，服务器能轻松应对。当请求量增大的时候，服务器的资源已经扛不住这种压力了，从而将相关的应用放在不同的服务器上，提供更好的性能，当请求量进一步增大的时候，应用jboss和mysql可能都不能抗住这种请求压力了，从而也就引出了集群的由来

```
