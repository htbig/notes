# Think Space

hello everyone, i'm ht,ozdkHzDCAAHQpWdFq93b

## [安卓相关知识](/directory/android.md)
## [考勤机](/directory/考勤机.md)
## [性能监控](/directory/性能监控.md)
## [ERP系统](/directory/ERP.md)
## [azure iot](/directory/azure.md)
## [CI/CD自动化发布版本](/directory/autoreleaseapp.md)
## [OpenNetworkLinux](/directory/ONL.md)

# 2019 Mar
- [firefly linux烧image](#firefly烧写)
- [数据库操作](#sql_op)
- [ssh反向隧道](#ssh)
- [git修改远端origin](#git_origin)
- [docker编译](#docker_compile)
- [gitlab-ci](#gitlab-ci)
- [syslog安装](#syslog-ng)
- [集群分布式](#集群分布式)
- [启动虚拟机](#启动虚拟机)
- [mirror link](#mirror_link)
- [frp](#frp)
- [路由添加](#路由添加)
- [查看上电时间](#查看上电时间)
- [启动django](#启动django)
- [路由转发](#路由转发)
- [systemd-timesyncd](#systemd-timesyncd)
- [go语言](#go语言)
- [测试网速](#测试网速)
- [ubuntu服务](#ubuntu服务)
- [ubuntu deb包制作](#deb包制作)
- [bash for循环](#bash_for)
- [html_upload](#html_upload)
- [字符串替换](#字符串替换)
- [查询文件夹大小](#文件大小命令)
- [检查磁盘坏块](#检查磁盘坏块)
- [识别磁盘类型](#识别磁盘类型)
- [gdb调试](#gdb调试)
- [免费签名](#免费签名)
- [查看cpu温度](#查看cpu温度)
- [映射windows目录到linux](#映射windows目录到linux)
- [cpi火焰图]（#CPI火焰图）                   
* 将制作的tf卡内容dd到image文件，然后在dd回其他tf卡
```
dd if=/dev/sdc of=/home/ht/image/image_16
dd if=/home/ht/image/image_16 of=/dev/sdc(速度5M，6M多)
sudo watch -n 5 pkill -USR1 ^dd查看dd 进度
```
* 制作虚拟文件系统
```
32位
dd if=/dev/zero of=/home/ustar/fat32 bs=1G count=10
mkdir /home/ht/ONL
mkfs.vfat fat32
sudo mount -o rw -t vfat /home/ustar/fat32 /home/ustar/ONL
64位
sudo dd if=/dev/zero of=/volb/ht/xfs bs=1G count=100
sudo mkfs.xfs -n version=ci /volb/ht/xfs
sudo mount xfs test/
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
* postgresql:
```
1:initdb -D /home/ht/opt/postgresql/data/
[ht@work ~]$ initdb -D ./opt/postgresql/data
The files belonging to this database system will be owned by user "ht".
This user must also own the server process.

The database cluster will be initialized with locale "C".
The default database encoding has accordingly been set to "SQL_ASCII".
The default text search configuration will be set to "english".

Data page checksums are disabled.

creating directory ./opt/postgresql/data ... ok
creating subdirectories ... ok
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting dynamic shared memory implementation ... posix
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... ok
syncing data to disk ... ok

WARNING: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    pg_ctl -D ./opt/postgresql/data -l logfile start

2:pg_ctl -D /home/ht/opt/postgresql/data/ -l logfile start
这个时候ps auxf
ht       11231  0.1  0.1 167368 15984 pts/0    S    18:15   0:00 /usr/local/pgsql/bin/postgres -D /home/ht/opt
ht       11233  0.0  0.0 167368  2616 ?        Ss   18:15   0:00  \_ postgres: checkpointer process   
ht       11234  0.0  0.0 167368  2616 ?        Ss   18:15   0:00  \_ postgres: writer process   
ht       11235  0.0  0.0 167368  2616 ?        Ss   18:15   0:00  \_ postgres: wal writer process   
ht       11236  0.0  0.0 167676  3796 ?        Ss   18:15   0:00  \_ postgres: autovacuum launcher process   
ht       11237  0.0  0.0  22384  2004 ?        Ss   18:15   0:00  \_ postgres: stats collector process
[admin@work ~]$ psql -U admin postgres
psql (9.6.5)
Type "help" for help.

postgres=# help
You are using psql, the command-line interface to PostgreSQL.
Type:  \copyright for distribution terms
       \h for help with SQL commands
       \? for help with psql commands
       \g or terminate with semicolon to execute query
       \q to quit

createdb -U admin UTCloudApi_develop创建数据库
psql -U admin UTCloudApi_develop < db/UTCloudApi_develop.sql导入数据库
dropdb UTCloudApi_develop删掉DB
psql -h 10.4.32.180 -U admin -d store_db
psql -d cloud_db -f 1.sql执行1.sql里的语句
```
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
如果ssh 断开就反向隧道结束，所以用autossh:
autossh -M 5678 -NR 1234:localhost:2223 user1@123.123.123.123 -p22利用5678端口来监听22连接，断了就重连，autosh默认后台执行
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
#启动虚拟机
```
root@vbg-m8105:~# virsh 
Welcome to virsh, the virtualization interactive terminal.

Type:  'help' for help with commands
       'quit' to quit

virsh # list 
 Id    Name                           State
----------------------------------------------------
 3     ArchLinux-24.ovf               running
 4     ArchLinux-3.ovf                running
 5     ArchLinux-4.ovf                running
 6     ArchLinux-5.ovf                running
 7     ArchLinux-6.ovf                running
 8     ArchLinux-7.ovf                running

virsh # start ArchLinux-1.ovf
Domain ArchLinux-1.ovf started

virsh # start ArchLinux-2.ovf
Domain ArchLinux-2.ovf started

virsh # list
 Id    Name                           State
----------------------------------------------------
 3     ArchLinux-24.ovf               running
 4     ArchLinux-3.ovf                running
 5     ArchLinux-4.ovf                running
 6     ArchLinux-5.ovf                running
 7     ArchLinux-6.ovf                running
 8     ArchLinux-7.ovf                running
 9    ArchLinux-1.ovf                running
 10    ArchLinux-2.ovf                running
```
# mirror_link
```
http://mirror.mozy.com/archlinux/core/os/x86_64/历史版本查找链接
```
# frp
* frp研究
v0.10.0版本之前 客户端和服务器端需要为每一个用户请求创建一个连接，每个用户是指在frpc.ini文件里的每一个section都会建立一个tcp链接
之后的版本只会在客户端和服务端建立一个tcp链接，tcp多路复用,然后起4个goroutine去做不同的事情：阻塞的等待svr.closedCh,controler一直注意closedCh的状态，发现旧的连接关掉会建立一个新的连接。每隔10s检查代理注册是否有，读controller的closedCh是否有数据，决定是否重连接到frps服务，manager handles all channel events and do corresponding process默认30s心跳定时器，1s心跳检查定时器，每隔30s像sendCh发送一个ping包，该包会被writer读走，发送给frps服务，每隔1s会检查有个pong时间间隔是否超过了90秒，超过了就会将ctl.conn关掉，这样会使得reader停掉，读从frps端发过来的包，更新pong时间，
每隔30s心跳交互一次
# 路由添加
```
在内网pc机添加路由到10.4.32.0网段
mac  查看路由命令：netstat -nr
mac：sudo route -n add -net 10.4.32.0 -netmask 255.255.254.0 172.21.30.127
sudo route -n add -net 114.114.114.0 -netmask 255.255.255.0 192.168.166.100
mac:删路由  sudo route -v delete -net 10.4.32.0 -gateway 172.21.30.127 -netmask 255.255.254.0
route add -net 10.4.32.0 netmask 255.255.254.0 gw 172.21.30.127
windowns:
route add 10.4.32.0 mask 255.255.254.0 172.21.30.127 -p
```
# 启动django
```
./manage.py runserver 0.0.0.0:8000 --settings=dc.settings.develop
./manage.py runserver 0.0.0.0:8001 --insecure
```
# 查看上电时间
```
查看系统上电起来时间：
date -d "$(awk -F. '{print $1}' /proc/uptime) second ago" +"%Y-%m-%d %H:%M:%S"
```
# systemd-timesyncd
* systemd-timesyncd每次收到一个新的NTP同步请求时，后台服务就把当前时间保存到磁盘，并尽可能在系统启动时修正系统时间，这样处理的目的是为了适应像Raspberry Pi和嵌入式设备这种缺少 RTC 的系统，并确保这些系统时单点处理。

# go语言
* goconvey golang自动测试框架：go get github.com/smartystreets/goconvey
* go语法
```
import包下划线_表示将导入的包init函数执行一遍
import .表示引入的该包可以不用带包前缀，不建议用这种方式，
import f "fmt"这个f就是别名操作
```
* go map
```
go语言map结构存储情况：
其中0xc420074120是map地址，格式是下方count=2的顺序，其中0xc420074130的地址开始的8个字节为buckets地址，打印出来0xc42007c000，结构是tophash 的结构。map的unmasel会将弄一个空的map,buckets，然后将key-value一个一个拷贝过去，
(gdb) x/20x 0xc420074120
0xc420074120:   0x00000002      0x00000000      0x00000000      0x3c60991d
0xc420074130:   0x2007c000      0x000000c4      0x00000000      0x00000000
0xc420074140:   0x00000000      0x00000000      0x00000000      0x00000000
0xc420074150:   0x00000000      0x00000000      0x00000000      0x00000000
0xc420074160:   0x00000000      0x00000000      0x00000000      0x00000000

(gdb) x/20x 0xc42007c000
0xc42007c000:   0x00005c38      0x00000000      0x004e258d      0x00000000
0xc42007c010:   0x00000001      0x00000000      0x004e25fe      0x00000000
0xc42007c020:   0x00000002      0x00000000      0x00000000      0x00000000
0xc42007c030:   0x00000000      0x00000000      0x00000000      0x00000000
0xc42007c040:   0x00000000      0x00000000      0x00000000      0x00000000

count = 2, flags = 0 '\000', B = 0 '\000', noverflow = 0, hash0 = 1012963613, buckets = 0xc42007c000,
  oldbuckets = 0x0, nevacuate = 0, extra = 0x0

{tophash = "8\\\000\000\000\000\000", keys = {0x4e258d "h", 0x4e25fe "ee", 0x0 "", 0x0 "", 0x0 "",
    0x0 "", 0x0 "", 0x0 ""}, values = {{test = 0x4e2632 "123", Name = 0x0 "", Order = 0x0 ""}, {
      test = 0x4e2632 "123", Name = 0x0 "", Order = 0x0 ""}, {test = 0x0 "", Name = 0x0 "", Order = 0x0 ""},
    {test = 0x0 "", Name = 0x0 "", Order = 0x0 ""}, {test = 0x0 "", Name = 0x0 "", Order = 0x0 ""}, {
      test = 0x0 "", Name = 0x0 "", Order = 0x0 ""}, {test = 0x0 "", Name = 0x0 "", Order = 0x0 ""}, {
      test = 0x0 "", Name = 0x0 "", Order = 0x0 ""}}, overflow = 0x0}
```
# 测试网速
```
测速命令：
curl -w %{time_namelookup}::%{time_connect}::%{time_starttransfer}::%{time_total}::%{speed_download}"\n" -sX POST   http://ip:port/api/get_dev_info   -H 'Authorization: Basic '   -H 'Cache-Control: no-cache'   -H 'Content-Type: application/json'   -H 'Postman-Token: 36f1138d-40c2-1a97-3879-0b7a51dc0335'   -d '{"devname":"xxx"}'
```
# ubuntu服务
```
1：ubuntu下启动与关闭服务的脚本存放与/etc/rc[?].d目录下
2：如果打开/etc/rc[?].d目录，会发现这些目录下的文件都是形如Snnxxxx或Knnxxxx的符号链接，而且都是指向/etc/init.d
3：其中链接文件中以S开头的表示在调用/etc/init.d目录中对应脚本的时候会传递一个start参数，也就是启动对应服务，而以K开头的则是传递一个stop参数，由此关闭此服务，此处的K表示kill
4：S和K后面的nn是一个数字，表示本脚本被执行的先后顺序，小号在前大号在后，这样以解决不同服务之间可能存在的先后依赖关系
5：另外，除了/etc/rc[0~6].d文件外，还有一个/etc/rcS.d目录，这个目录下的服务脚本与/etc/rc[0~6].d格式类似，也为指向/etc/init.d中的脚本的链接，但是会在/etc/rc[0~6].d中的脚本执行前首先被执行
根据上面的介绍，如何将一个软件安装为服务也就比较清楚了，那就是在/etc/init.d添加一个服务的启动脚本，然后在需要启动服务的对应级别的/etc/rc[0~6].d中按照文件名格式添加一个指向/etc/init.d中脚本的符号链接
以apache2为例，默认情况下，apache2编译安装在/usr/local/apache2，apache2的服务器启动脚本是/usr /local/apache2/bin/apachectl，那么安装服务就是要把此apachectl拷贝到需要启动apache2服务器的运行级别对应的/etc/rc[?].d目录下，一半来说ubuntu是运行在级别2下，所以也就是拷到/etc/rc2.d下，命令如下：
sudo cp /usr/local/apache2/bin/apachectl /etc/init.d/httpd
在这里，我们把拷贝的脚本文件名改为惯用的httpd，那么在系统服务中apache2的名称也就是httpd。
手动添加服务的话，就是要在/etc/rc2.d里为httpd作一个形如Snnxxxx的链接。假定启动顺序我们定为80，那么就是要建立一个指向/etc/init.d/httpd的链接/etc/rc2.d/S80httpd，命令如下：
sudo ln -s /etc/init.d/httpd /etc/rc2.d/S80httpd
然后用sysv-rc-conf或者chkconfig –list检查一下就能看到已经在运行级别2下建立的名为httpd的服务，重启后，系统会自动启动apache2。
现在要手动启动、关闭或重启httpd服务器可以使用service命令+服务名+参数的形式，如下三句分别是启动、关闭和重启apache2服务器的命令：
service httpd start
service httpd stop
service httpd restart
要安装服务除了以上手动作链接外，还可以使用一些工具软件来实现，比如常用的有update-rc.d、chkconfig和sysv-rc- conf等
```
# deb包制作
* dpkg-deb命令
```
1：创建包的工作目录：test
2：在test目录添加DEBIAN目录，存放控制信息，文件名字叫control，内容为：
Package: electronic-wechat
Version: 1.4.0-2016.08.24
Section: BioInfoServ
Priority: optional
Depends:
Suggests:
Architecture: i386
Installed-Size: 4096
Maintainer: gatieme
Provides: bioinfoserv-arb
Description: A better WeChat on macOS and Linux. Built with Electron by Zhongyi Tong
其中Architecture说明此包在什么cpu下安装，是一个判断条件
3：在test下创建usr/bin目录，存放我的二进制文件main，这个目录结构安装deb包的时候会安装到根目录的/usr/bin下
4：回到test上一级目录，利用dpkg-deb -b test test.deb生成test.deb包
5：为了测试test.deb包，可以dpkg -i test.deb就可以将main拷贝到/usr/bin目录下，bash下执行main就可以输出hello world了，包制作成功。
```
* checkinstall制作deb包
```
1:git clone http://heting@172.21.148.250:10080/veg/vega.git
2:make && make install
3: checkinstall -D --pkgname=vega --pkgversion=2017-10-31 --install=no  --pkgsource=../../vega制作出包。
```
* 修改已经打包成deb的包
```
1：dpkg -X xxx.deb test #  解包安装内容
2：cd test
3:dpkg -e ../xxx.deb #  解包控制信息
4：修改信息
5：重新打包：cd ../
dpkg -b dirname xxx_new.deb
```

# bash_for
```
for ((i=0; i<1000; ++i))  ; do    curl -X GET   http://172.21.78.50:8082/monitor   -H 'authorization: Basic aHQ6MQ=='   -H 'cache-control: no-cache'   -H 'content-type: application/json' ; done
```
# html_upload
```
上传文件multipart/form-data
http上传文件到系统内存，大于默认大小。会存在临时文件里
```

# 字符串替换
```
sed -i "s/zhangsan/lisi/g" `grep zhangsan -rl /modules`
```

#vim 替换字符串
```
:%s/\r//g

% 加上这个，表示操作全文的“每一行”。（默认只操作当前一行）
s  替换命令：substitute
/\r 替换源是\r
/    替换为“空”
/g  整行所有的匹配都要替换。（默认只替换第一处）
```

# 文件大小命令
* du -h --max-depth=0 user


# 检查磁盘坏块
*  badblocks -v /dev/sda10 

# 识别磁盘类型
```
root@u1:/dev# stat -c %T /dev/vda #minor number
0
root@u1:/dev# stat -c %t /dev/vda #major number
xx
root@u1:/dev# readlink -f /sys/dev/block/xx:0/device/driver
/sys/bus/virtio/drivers/virtio_blk
```
# gdb调试
```
gdb调试死锁的方法：
gdb 
attach pid
thread apply all bt

找到_lll_lock_wait 锁等待的地方。
然后查找该锁被哪个线程锁住了。
```
# 路由转发
```
实验步骤：

1:打开4g网络   start celular 

2:测试外网连通性   ping 114.114.114.114

3: 配置以太网ip    ifconfig eth0 192.168.166.100 netmask 255.255.255.0， 同时自己pc几也配置同一局域网ip：192.168.166.51

ping -I eth0 192.168.166.51

ping -I ppp0  114.114.114.114

上两条命令可以ping 通，但是ping 不加-I就无法ping 通，最后知道是ip rule走eth0 table ,不是main table,然后eth0 table里没有任何路由

4： 往eth0 table 里添加路由：

ip route list table default 

ip route list table main

ip route add default dev ppp0 table eth0

ip route add 10.64.64.64 dev ppp0 proto kernel src 10.97.96.255  table eth0

ip route add 192.168.166/24 dev eth0 proto kernel src 192.168.166.100  table eth0

这样ping 114.114.114.114,ping 192.168.166.51都可以ping通

5: 开始配置ip转发

查看命令：iptables -t nat -L
cat /proc/sys/net/ipv4/ip_forward默认为0
echo 1 > /proc/sys/net/ipv4/ip_forward或者sysctl -w net.ipv4.ip_forward=1
iptables -t nat -A POSTROUTING -s 192.168.166.0/24 -o ppp0 -j MASQUERADE
这样配置之后发现，自己上还是ping 114.114.114.114不通，在开发板上tcpdump -i ppp0 -Q inout也查看不到192.168.166.51过来的包，网上搜到说执行：
iptables -F
iptables -t nat -F
iptables -X
删掉之前的路由规则就可以，尝试了，确实可以ping通了，但是还是得找到根本原因。
最后发现是FORWARD chain 的natctrl_FORWARD有一条规则：
Chain natctrl_FORWARD (1 references)
target     prot opt source               destination
DROP       all  --  anywhere             anywhere
查看命令：iptables -L natctrl_FORWARD --line-numbers
删除命令：iptables -D natctrl_FORWARD 1
这样自己mac电脑终于可以通过开发版ip转发ping 114.114.114.114通了
```
# 免费签名
```
let's encrypt
```
# 查看cpu温度
```
cat /sys/class/thermal/thermal_zone0/temp | awk '{print "CPU Temp:"(int($0) / 1000)}
```
# 映射windows目录到linux
* 可以使用mount挂载
```
首先创建被挂载的目录：

$ mkdir windows

将共享文件夹挂载到windows文件夹：

$ sudo mount -t cifs -o username=TP11060,password=share //192.168.66.198/share ./windows

其中几个参数表示含义：

    cifs：Common Internet File System，可以理解为网络文件系统。
    usrname：访问共享文件夹的用户名,如TP11060
    password：访问密码
```
# CPI火焰图
```
https://yq.aliyun.com/articles/465499
```
