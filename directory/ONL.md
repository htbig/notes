### ONL
```
Open Network Linux（ONL）是用于裸机交换机的Linux发行版。onl构建了一个与onie兼容的安装程序和一个交换机映像，其中包含一个完整的debian发行版，添加了用于在裸机交换机上运行的驱动程序和配置。
```
### accton onl install
```
https://github.com/opennetworklinux/ONL/blob/master/docs/GettingStartedWedge.md
```
### onie安装onl
* ONIE:/ # ifconfig eth0 10.4.32.214/23
* ONIE:/ # echo "nameserver 8.8.8.8" > /etc/resolv.conf
* ONIE:/ # install_url http://xxxxx.installer
