# 制作ipk包
```
假如工程名为etsnetd
1:工程目录下有编译好的可执行文件etsnetd和control文件，control文件内容如下：

Package: etsnetd
Installed-Size:   56440
Filename: ./etsnetd.ipk
Version: 1.0.0
Priority: optional
Maintainer: xxx <xxx@xxx.cn>
Architecture: aarch64_cortex-a53
License: GPL
Description: etsnetd service

2: 工程目录需要有个etsnetd.service文件，用来将etsnetd以service的方式在设备上运行，内容可以参考如下：
#!/bin/sh /etc/rc.common
# Copyright (C) 2013 OpenWrt.org

# start after and stop set dependence use START and STOP
START=100
STOP=100
USE_PROCD=1  #use procd start.run service etsnet status need

PROG=/usr/bin/etsnetd

start_service()
{
	procd_open_instance
    procd_set_param command "$PROG"
    #procd_append_param command 100000
    procd_set_param respawn
    procd_close_instance
}

3:工程目录还会有一个build.sh脚本，内容如下，用来生成ipk包
mkdir -p data/usr/bin
mkdir -p data/etc/init.d
cp etsnetd data/usr/bin/
cp etsnetd.service data/etc/init.d/etsnetd
chmod +x data/etc/init.d/etsnetd
cd data
tar zcvf data.tar.gz *
rm -rf usr/
rm -rf etc/
cp ../control ./
tar zcvf control.tar.gz control
rm control
tar zcvf etsnetd.ipk ./*
当然build.sh脚本的内容可以写在makefile里用来生成ipk package
3:执行完build.sh脚本，data目录就会有一个etsnetd.ipk包了，可以通过opkg install etsnetd.ipk安装，
并且opkg info etsnetd查看安装版本的版本，其中control文件里可以控制版本号
```
