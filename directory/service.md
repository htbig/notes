# 服务管理
```
1: /etc/init.d下新建服务脚本文件，内容如下,以sleep为例，并且chmod +x该文件：

#!/bin/sh /etc/rc.common
# Copyright (C) 2013 OpenWrt.org

# start after and stop set dependence use START and STOP
START=100
STOP=100
USE_PROCD=1  #use procd start.run service sleep status need

PROG=/usr/sbin/sleep

start_service()
{
	procd_open_instance
    procd_set_param command "$PROG"
    procd_append_param command 100000
    procd_set_param respawn 5 1 -1 #${respawn_threshold:} ${respawn_timeout:} ${respawn_retry:}
    procd_close_instance
}
2: 将可执行文件拷贝到指定路径：/usr/sbin/
3:这样就可以service sleep start|stop|restart|status等一些命令了
4:实现开机自启动：
执行service sleep enable会建立软连接：
ln -s /etc/init.d/sleep /etc/rc.d/S100sleep
ln -s /etc/init.d/sleep /etc/rc.d/S100sleep
这样设备上电起来就可以自启动了
4:如何保证服务restart always呢？
其中respawn实现了服务进程被kill掉会重新调用起来（根据实际需求设置）
```
