# Think Space

hello everyone, i'm ht

# 2019 Mar
- [安卓编译](#android compile)

# android compile

#### 第三方apk进系统
* 在packages/apps目录下新建Prebuilt_apps目录，将需要安装的apk文件放到该目录，并且在该目录新建Android.mk文件，内容：
```
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_POST_PROCESS_COMMAND := $(shell cp -r $(LOCAL_PATH)/*.apk $(TARGET_OUT)/app/)
```
* 执行make -j8，会将Prebuild_apps的文件拷贝至out/target/product/roc_rk3328_cc_box/system/app,如果apk文件里包含so文件，解压apk，如其中lib(.so)文件，把 .so文件copy到system\lib下， 再编译，这样第三放apk就编译进system.img里了，会在/system/app目录下
* 将/system/app/Prebuilt_apps的文件拷贝到/data/app，这样上电起来，会安装data/app下的apk。
* 可以在/system/bin下新建run_apk.sh脚本，内容：
```
  busybox cp /system/app/xxx.apk data/app
  chmod 777 data/app/xxx.apk
```
* 在system/core/rootdir/init.rc添加内容：
```
service run_apk /system/bin/run_apk.sh 
class main 
oneshot
```
这样系统起来就会执行run_apk.sh并且安装第三方apk了

#### 让服务上电起来自动开启
* 添加start_app相关的东西用来上电自动启动服务，在device/rockchip/common/sepolicy/file_contexts加入：
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
* 添加device/rockchip/common/sepolicy/commontools.te文件，其内容：
```
wfx@OptiPlex-9020:~/proj/roc-rk3328-cc$ cat device/rockchip/common/sepolicy/commontools.te
type commontools, domain;
type commontools_exec, exec_type, file_type;
init_daemon_domain(commontools)
```
* 添加out/target/product/roc_rk3328_cc_box/system/bin/commontools文件，内容：
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
* 修改system/core/rootdir/init.rc：
```
git diff system/core/rootdir/init.rc
+service start_app /system/bin/start_app.sh
+    class main
+    user root
+    group root
+
```
