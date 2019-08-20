# Think Space

hello everyone, i'm ht,ozdkHzDCAAHQpWdFq93b

# 2019 Mar
- [安卓编译](#android_compile)
- [安卓开发](#android_develop)
- [安卓签名](#安卓签名)
- [安卓版本发布](#安卓版本发布)
- [安卓权限](#安卓权限)
- [安卓4g ppp上网域名不同](#域名不通)
- [安卓发送信号](#安卓发送broacast)
- [安卓查看端口使用](#安卓查看端口使用)
- [安卓命令行调试WIFI](#安卓命令行调试WIFI)

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

* build/core/version_defaults.mk下PLATFORM_VERSION := 7.1.2定义安卓版本
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
* 安卓中c程序编译
```
想实现一个守护进程在安卓系统检查各个服务运行情况，如何某服务退出就重新开启某服务，用c写的程序，直接gcc编译放在安卓系统/system/bin下执行，一直不正常，一些system,popen的执行都不对，最后改为 ndk-build，可以正常实现功能。查看相关知识点：https://developer.android.google.cn/ndk/guides?hl=zh-cn
```
```
logcat -s tag:priority
如：logcat -s ActivityManager:I
logcat输出量比较大，安卓把log输出到不同的缓存区，目前定义了4个：radio,system,event,main,应用的log都输给main，默认log输出不指定的话是输给main和system.缓存区是环形首尾相连结构，大小固定，会新log到了一定大小覆盖旧log的。
logcat –b system查看system的缓存区log，
logcat -c 清除缓存区log
logcat -d 输出到屏幕后退出
logcat -f xxx 输出到文件中，默认标准输出
logcat -g 输出缓存区大小


其中priority有V-(verbose) -明细 (最低优先级) • D-(debug)-调试• I-(information)-信息• W-(warning)-警告• E-(error)-错误
• F — 严重错误• S — 无记载
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
pm install -r app-debug.apk 替换已经安装的apk,不会删掉应用之前保存的db或者其他信息

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
mount /dev/sdb /var/root     tech上mount了3T的盘
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
```
pc端adb client连接adb server，adb server在跟设备端的adbd守护进程建立tcp连接
/sbin/adbd重新运行adbd
```
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
* 安卓上调试
```
gdbserver 192.168.50.17:6667 --attach 15619
target remote 192.168.50.17:6667
```
* 编译安卓uboot,kernel,sdk：
```
repo init --repo-url 
ssh://git@www.rockchip.com.cn/repo/rk/tools/repo -u ssh://git@www.rockchip.com.cn/gerrit/rk/platform/manifest -b android-7.0 -m rk3328_box_nougat_release.xml
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

* busybox wget http://xxx:port/apps/version
```

```
# 安卓签名
* 判断apk是否有签名
```
jarsigner -verify door-unsigned.apk

jar 已验证。
说明有签名
-verbose -certs加上可以查看具体签名情况
生成签名文件:
keytool -genkey -alias ustar-ai.keystore -keyalg RSA -validity 2000 -keystore newandroid.keystore
keytool -importkeystore -srckeystore newandroid.keystore -destkeystore newandroid.keystore -deststoretype pkcs12
keytool -list -v -keystore newandroid.keystore查看签名信息
jarsigner -verbose -keystore ./newandroid.keystore  -signedjar app-release_mysigned.apk door-unsigned.apk ustar-ai.keystore(别名)

```

echo 'export PATH=~/tools:$PATH' >> ~/.bashrc

# 安卓版本发布
```
我们一般在发布app之前，都会设置版本号。versionCode和versionName。简单解释一下：


android:versionCode:主要是用于版本升级所用，是INT类型的，第一个版本定义为1，以后递增，这样只要判断该值就能确定是否需要升级，该值不显示给用户。
android:versionName:这个是我们常说明的版本号，由三部分组成<major>.<minor>.<point>,该值是个字符串，可以显示给用户。
```
# 安卓权限
* 在init.rc中添加上电起来自动执行脚本，脚本里对/system目录的操作是没有权限的，对/data操作可以

# 域名不通
```
添加dns: ndc resolver setnetdns ppp0 114.114.114.114 8.8.8.8
```
# 安卓发送broacast
* am broadcast -a android.intent.action.BOOT_COMPLETED
```
在/data/system/users/0/package-restrictions.xml会记录apk的stop状态，如果该apk从来没有启动或者被用户强制关掉过，就会处于stopped state状态，就不会接受到broadcast。
遇到三方编写的apk包无法上电接受BOOT_COMPLETED实现自动重启，网上搜索了一堆方法都不行，于是开始看源码，发现frameworks/base/services/core/java/com/android/server/am/ActivityManagerService.java文件里有关于broadcast广播的判断:
if(((info.flags & ApplicationInfo.FLAG_SYSTEM) ==0)&&("broadcast".equals(hostingType)))
                {
                        if(DEBUG_LOWMEM)Slog.v("xzj", "third part process dont start for broadcast: " + info.uid + "/" + info.processName);
                        return null;
                }
                所以第三方apk无法接受到broadcast，我将return null屏蔽，重新编译了系统，可以实现自己写的apk上电自动启动了
```
# 安卓查看端口使用
* netstat -anp|grep 8088会有进程名

# firefly烧写
* sudo upgrade_tool di -b ./boot.img
* sudo upgrade_tool di -k ./kernel.img
* sudo upgrade_tool di -s ./system.img
* sudo upgrade_tool di -r ./recovery.img
* sudo upgrade_tool di -m ./misc.img
* sudo upgrade_tool di resource ./resource.img
* sudo upgrade_tool di -p ./paramater.txt   #烧写 parameter
* sudo upgrade_tool ul ./MiniLoaderAll.bin # 烧写 bootloader
# 安卓命令行调试WIFI
* 开启wifi: svc wifi enable
* 关闭wifi: svc wifi disable
* 查看网络：dumpsys netstats detail：
```
Active interfaces:
  iface=eth0 ident=[{type=ETHERNET, subType=COMBINED, metered=false}]
  iface=wlan0 ident=[{type=WIFI, subType=COMBINED, networkId="BCM2017", metered=false}]
Active UID interfaces:
  iface=eth0 ident=[{type=ETHERNET, subType=COMBINED, metered=false}]
  iface=wlan0 ident=[{type=WIFI, subType=COMBINED, networkId="BCM2017", metered=false}]
```
* 查询所有WI-FI ap
```
   wpa_cli scan
   wpa_cli scan_results
```
* 查询Wi-Fi状态：wpa_cli status
* 增加Wi-Fi：
```
wpa_cli add_network
wpa_cli set_network 0 ssid '"SSID"'
wpa_cli set_network 0 psk '"PASSPHRASE"'
```
* 连接Wi-Fi：wpa_cli enable_network 0
