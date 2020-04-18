# 制作ipk包
```
假如工程名为aaa
1:该目录下可以有编译好的可执行文件aaa和control文件，control文件内容如下：

Package: aaa
Installed-Size:   56440
Filename: ./aaa.ipk
Version: 2.0.0
Priority: optional
Section: qpe/applications
Maintainer: Dafydd Walters <dwalters@users.sourceforge.net>
Architecture: x86_64
License: GPL
Description: test for aaa
2:工程目录还会有一个build.sh脚本，内容如下，用来生成ipk包
mkdir -p data/usr/bin
cp aaa data/usr/bin/
cd data
tar zcvf data.tar.gz *
rm -rf usr/
cp ../control ./
tar zcvf control.tar.gz control
rm control
tar zcvf aaa.ipk ./*
3:执行完build.sh脚本，data目录就会有一个aaa.ipk包了，可以通过opkg install aaa.ipk安装，
并且opkg info aaa查看安装版本的版本，其中control文件里可以控制版本号
```
