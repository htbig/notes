# iot
* 物联网套件的选择:微软和阿里百度等
```
1 地域选择:阿里百度国内,微软国外,到后来需要用到微软时候确实也发现微软的很多iot功能都是在国际分支有实现,中国版没有的.
2 业务重点:微软和百度重点都在数据采集后的存储,分析,展现.以及对数据的分析,人工智能为重点,重点不在接入;阿里的物联网套
件重点一是设备接入,二是数据的导出
3 数据安全性:微软和阿里都是采用ssl加密才能云端和设备端通信,百度不强制.特别阿里要求更高,一定要ssl版本为TLSV1.1 或 
TLSV1.2 版本.
```
* 物联网套件协议选择:mqtt.https.amqp.coap(仅支持udp协议,且仅仅华东2上海地区支持coap)
```
阿里iot支持mqtt/coap/http,其中只有mqtt支持远程控制设备;且mqtt是基于tcp/ip协议栈的异步通信消息协议,适用于设备硬件存
储有限,或者网络不稳定的情况,阿里iot且在原生mqtt topic基础上实现了rrpc(Revert Remote Procedure Call)同步通讯方式,
微软是derict method方式.
还支持http协议,https保证通道安全,它只适合单纯的数据上报场景,显然不适合我们需要C2D的场景
通过ack保证消息被发出去，qos1可能会发多次，解决方法：将收到ack重发的超时时间设置keepAliveInterval*2。断网不可怕，只要
iotkit服务不重启，消息都会存在内存里，网络一通就可以继续发送，不会丢数据。
```
* 何为nb-iot
```
Narrow Band Internet of Things, NB-IoT
```
* 何为websocket
```
平时使用的http协议,只能客户端发起,服务端无法主动向客户端发起请求,websocket就是为解决这个而产生.websocket协议客户端,服
务端都可主动推送信息,可以文本或者二进制文件
```
* node js/pyhton
```
node 安装模块用npm install
python安装模块用pip install 
dpkg -i **
pm install -r **
flask框架是python写的轻量级的web框架
express框架是node js实现的快速，开放，极简的web框架
events:eventEmitter(on,removeAllListeners,once...)/stream(createReadStream等)/net(tcpServer)
async await .then .catch try{}catch{}
ejs模版引擎
资源全放public目录，包括图片,css文件等
label
select
button
div(分区)
<%js代码%>代表js 代码
node-cron.schedule可以执行定时任务
简单说promise就是用来执行异步任务的，可以解决上面所说的回调地狱问题
then-catch-finally
node --prof 记录堆栈信息在log
node --prof-process log > txt,由不可读变为可读
当接口很复杂的时候，GC肯定会变大，当GC占用过多的时候，就要考虑对代码格式进行优化，比如声明过多的变量，导致GC明显增大；这是可以考虑使用变量复用，前面声明的变量生命周期已经结束来，就可以考虑在后面需要声明新变量的时候，复用前面的变量名，唯一的缺点就是，变量的复用，会使得代码可读性变差，这就需要详细的代码注视了。这样一定程度可以减小GC的占用，因为当GC运行的时候，程序是不能做其它任何事情的
查看耗时的地方，进行异步优化
setTimeout
setInterval
```
* 如何保证数据安全ssl(secure socket layer)/tsl(transport secure layer) 协议知识
```
加密证书:可以将明文散列出一段文字(MD5或者sha),然后再利用私钥对这段文字加密就可以得到证书;

```
* 如何处理网络压力
```
负载均衡方式1:基于特定服务器软件的负载均衡,http支持location,2:基于dns负载均衡,对于同一个域名配置多个不同的地址,最终查询
这个名字的域名得到其中一个地址,访问不同服务器达到负载均衡3:反向代理负载均衡,代理服务器将请求均匀的发送给内部web服务器,不会
出现负载在某个内部服务器特别集中的现象,缺点代理服务器成为了瓶颈4:基于NAT(网络地址转发)负载均衡技术;
nigix利用upstream机制,利用反向代理方式运行,将客户端的web请求根据一定规则发送给后端不同的服务器上去处理
```
* 容灾性
```
当系统遭受到外界破坏是能及时切换到系统的备份系统.能够提供系统恢复功能.
```
* beego开源框架实现告警模块
```
go语言实现,MVC架构,开发文档多,详细.数据库选择sqlite
op_id用于计数,实现云端和设备端大小比较获得同步状态,se_id用来记录同样告警的流水信息.
需要告警信息表,操作记录表,流水表等,
```
* 如何防止同一时刻很多告警同时上报上来问题
```
两方面考虑,1:从告警源头来解决,如果有告警上报消失频率高,说明告警产生模块编写就不太正常,做出修改;设备端的告警模块也会做
筛选,如果该告警已经上报过,存在设备端数据库,是不会重复上报给云端的
2:设备端发送到云端接受都是按照顺序接受的,所以一般不会出现不同步,除非是一条告警还没结束处理(即op_id还没有+1),下一条同设备的
告警就上报上来了(这种属于设备端发送告警频率过于频繁,需要修改上报告警的模块);
```
* Django框架
```
python实现,处理高请求问题,数据库同步,可以采用乐观锁,乐观锁并不是真正的锁,而是在更新数据库之前查询数据库数据是否是之前查询
到的值,否则不更新数据库;
命令:1 django-admin startproject project_name创建工程2:python manage.py startapp app_name新建app
3:python manage.py makemigrations创建更改文件,会生成py文件4:python manage.py migrate将生成的py文件应用的数据库
5:python manage.py runserver 0.0.0.0:8000启动服务6:python manage.py createsuperuser创建
用户7:python manage.py dbshell进入数据库8:python manage.py可以看到更多命令
```
* 告警及事件
```
告警比如摄像头离线,上线后告警消失
告警事件比如门锁打开失败,不存在这次门锁打开成功,告警消失,定为事件
```
* 几种关系数据库对比
```
   分布式数据库结和数据库技术和网络技术的产物，由一组数据组成这组数据分布在不同计算机上那个节点通过网络,有独立处理能力，
成为场地自治，可以执行局部应用，也可以通过网络通信子系统执行全局应用，分布式数据库中的数据相对于用户透明;
   数据库备份分为物理备份和逻辑备份，物理备份就是操作系统层面对数据文件进行备份，冷备份和热备份，冷备份是将数据库正常
关闭，在停止状态下利用copy cp tar cpio等命令将数据库文件备份下来，当数据库发生故障时，拷贝回来，进行恢复，热备份也
分为两种，一种不关闭数据库，将数据库中需要备份的数据文件依次次至于备份状态，相对保持静止，然后copy cp tar cpio将
文件备份下来,备份完毕再将数据文件恢复为正常状态，另一种方式是利用备份软件在数据库正常运行状态下将数据库文件备份出来,
逻辑备份是利用各数据库系统自带工具软件备份和恢复数据库内容
sqlite基于文件的数据库,很好迁移性,不需要port socket交互,没有用户管理，
数据库和文件系统差别（acld），1:易用性，查找方便2:保证数据的完整正确有效一致3:并发控制好，4:数据的持久性（比如统计一个班上
分数80份以上的，如何文件系统，你得自己写代码分析，数据库直接用就好）
```
* app管理
```
所有服务启动关闭systemctl控制,包的安装dpkg -i或者pm install -r,不同类型的设备会有一个软件版本table,有不同运行软件,
所以会上线拿到设备类型及软件列表,且存放到本地json文件,且启动不同服务,如果软件控制服务启动是网络没通,会先从设备上json文
件load配置启动不同服务.软件版本通过wget到本地安装;界面可以完全控制设备上的软件模块开启关闭,除了涉及设备上线的重点服务
不允许管理员操作,以免误操作.
```
* gitlab ci/cd
```
软件版本持续集成和持续交付,持续以为随时可运行,不代表一直运行,持续集成就是代码的变更后自动检测,拉取,构建和进行单元测试的过
程;持续交付是指整个流程链,它自动检测代码变动并通过构建,测试,打包和相关操作运行他们以生成可部署的版本,基本没有人为干涉,持
续交付在软件中目标是自动化,效率,可靠性,可重复性和质量保证.我们应用中是提交代码,触发编译deb包,如果修改代码的特定文件即为
发布release版本,并将版本包上传了文件服务器供升级用.gitlab-runner跑在编译版本服务器上，运行注册到gitlab服务器，在
gitlab工程编写.gitlab-ci.yml文件，且设置中ci/cd选择gitlab-runner,完成整个持续交付流程。
```
* 全链路状态监控
```
服务端通过ping/pong检查服务正确性,设备端是服务定时查询设备在线情况,不在线发起重连.
```
* 内存
```
虚拟内存都是offset，根据selector查gdt得到base address,然后加上offset变成Linear地址，再经过mmu转换变成实际
物理地址。KERBASE为0xf0000000，到0xffffffff有256M，给内核使用，ULIM为0xef800000，以上的地址用户无法访问，
kernel可以读写。UTOP为0xeec00000，在UTOP和ULIM之间的空间内核和用户都只能读不能写。共12M，方内核的一些只读
结构。在UTOP以下的给用户用的。envs 设定1024个，1左移动10位所有的envs都放在env_free_list，curenv表示当前
运行的用户程序，刚boot起来他位NULL ,UVPT为ULIM减4M放env 自己的page table256个中断异常描述项在中断描述表其
实地址放在（IDT寄存器里），0-31的中断，Intel自己预留，当发生中断或者异常时,cpu根据中断向量索引找到中断处理函数
的地址T_SYSCALL 48位系统调用中断中断向量到中断地址过程:用户发生中断,cpu会根据IDT找到中断描述表的地址,在根据中
断矢量(中断号)找到中断门描述符,在根据里面的段选择子在GDT中找到基地址,再加上偏移地址,就得到中断处理函数的地址.中断
处理函数执行前,得找到一个地方存放中断发生前的一些寄存器状态信息,如EIP,CS等,这样中断处理完可以继续中断前的地方执行.
这个区域不能被用户程序访问到,所以x86遇到用户中断从用户态到内核态时,会将堆栈也切换到内核栈;tss就是存放这个堆栈位置
的结构,包括堆栈的段选择子和地址等;发生特权级别切换是,切换到内核栈后,cpu会将SS, ESP, EFLAGS, CS, EIP压栈,并且
将中断门描述符中对应的值放到CS, EIP,并且赋值正确的ESP和SS指向新的栈.TSS的ESP0和SS0设置为内核栈地址cpu可以处理
来自用户态和内核态的中断或者异常,内核态发生中断/异常,不需要切换堆栈,只需要push  EFLAGS, CS, EIP的值,不需要SS
和ESP的值;jos中用int $30触发cpu中断完成系统调用:如果为系统调用会调用lib/syscall.c下传入不同的
syscall_no(%eax存放)其他参数分别用%edx, %ecx, %ebx, %edi, 和%esi保存,不同的trap也有不同的trap_no,进入内核
栈,组件trap_frame之后调用trap函数来根据不同的trap_no,分配给不同的处理函数去执行.如果是系统调用kern/syscall.c
的syscall根据不同的syscall_no调用不同的函数实现,结果通过寄存器eax返回给用户态
```
* smp
```
symmetric multiprocessing,指所有处理器地位是对等的,包括内存对称和IO对称,虽然对等,但是他们也分为bsp处理器
(负责初始化其他cpu)和ap处理器,至于哪个是bsp由bios决定.
Advanced Programmable Interrupt Controller(高级可编程中断控制器)分为两个单元:LAPIC和IO APIC,他们通过
ICC相连.
作用1:减轻内存总线关于中断向量流量,2:可以在多处理器里面分担中断处理负载.LAPIC提供interprocessor interrupts 
(IPIs),允许任意处理器中断其他处理器或者设置其他处理器，有好几种类型的IPIs，
如INIT IPIs和STARTUP IPIs,LAPIC和IO APIC可以是独立的芯片,也可以将LAPIC和CPU放在同一个芯片里.
在SMP系统中,每个CPU伴随着一个LAPIC单元,用于传递和响应系统中断,也为与相连的CPU提供了唯一的ID
MMIOBASE(0xef800000)开始溜了4M给MMIO使用
bsp cpu将apentry的汇编拷贝到MPENTRY_PADDR（0x7000）,然后告诉汇编里面每个cpu用什么堆栈，然后向每个ap发送
ipi中断，告诉他entry.s的地址去执行，类似boot.s，最后调用到mp_main，最后将cpu状态设置为CPU_STARTED；这样
boot_aps就可以退出while循环继续初始化下一个cpu多cpu工作同时进内核，采用大锁，只能一个cpu运行在内核态，
```
* ACID
```
在可靠数据库管理系统中，事务具备的4个属性原子性，一致性，隔离性，持久性。
```
* 时序数据库和关系数据库
```
时序数据库以时间为横坐标，一些特性在不同时间点的数据记录，当然关系数据库也可以加上timestamp属性来实现，但是海量
数据性能上就不合适，时序数据库转本为这个海量数据的读写设计，时序数据库是写多读少，且会按照时间分段查找。会有内存写，
之后在往磁盘顺序写入来提高写性能，大量数据一般写在一个设备上不太现实，需要分布式存储，一般都以metric（类似table）和tag(不变属性入城市)方式将
数据存储到不同的设备，如果长时间段的查询可以将时间段分为好几段在不同设备上查询，并行提高性能。
```
* 文件系统
```
用户态文件系统，内核态文件系统，随便修改一个小功能都测试周期长，比较麻烦所以出现了FUSE。增加文件系统实现的灵活行，有利于再次开发
```
* ret iret
```
ret普通函数调用返回，弹出eip，iret中断调用返回会有cs等寄存器弹出
```
* fork过程
```
过程：
1: 创建sys_exofork一个空的新进程，这个时候设置的子进程env_status为mot_runnable,
2: 然后将父进程的所有地址空间（虚拟地址物理地址对应关系）全部拷贝到子进程，设置子进程状态为runnable，这样子进程就可以被sheduled_yield调度。
拷贝方法：
在子进程addr虚拟地址分配物理地址映射，然后将子进程addr地址map到父进程UTEMP地址，这样父进程访问UTEMP就是访问子进程的addr的物理地址，将父进程
的addr的内容拷贝到UTMP,然后unmap UTEMP的页，让UTEMP供以后使用，也以免父进程错误操作UTEMP，对应子进程的物理内存页。
copy on write
所有的页只是map一下，🔒指向的物理内存还是同一块。
```
* 页面错误处理过程
```
父子进程任意一个访问的共享内存，会促发页面错误处理，cr2寄存器会存储出错的虚拟地址，进入内核，内核会判断进程有没有设置page_fault_upcall，没有，
进程destroy掉，有的话，组建UTrapframe,切换用户进程栈到异常栈，将eip设置为env_pgfault_upcall，为一段汇编代码，最后会执行到c代码的pgfault,
将对应错误虚拟地址的内容拷贝到本进程，然后再退回到进程正常栈会继续出错地方去执行。
trap
```
* 中断
```
int指令产生的软中断是不可屏蔽的，时钟中断是可以屏蔽的，受eflags控制
```
* 网络从上到下分为
```
物理层，数据链路层，网络层（ip icmp igmp arp rarp物理地址到ip转换），传输层（tcp udp），会话层，
表示层，应用层（http ftp）
```
* call jmp区别
 ```
 call=push+jmp
 ```
 *  rpc
 ```
 就是不同设备上的进程间如何函数调用，相互协商好标准就好。
 ```
 * 实模式，保护模式
 ```
 实模式8086时期20根数据总线，最多访问1m空间，没有权限分级，寄存器实16位，不能访问整个1m空间，所以实现了内存分段，16位段寄存器左移4位于偏移地址相加20位，1M空间，这模式，程序员面临的直接物理地址，系统程序和用户程序权限没有区分；保护模式就是32位模式：访问4g空间
 ```
* 内存断点
```
直接在需要停住的地址watch
```
* 序列化和反序列化
```
将对象转换为字节流称为序列化，将字节流转换为对象称为反序列化，需要将数据在网络传输或者存储在文件中时，需要做序列化处理。
```
* linux运行exe
```
apt-get install wine
wget http://download.microsoft.com/download/1/1/1/1116b75a-9ec3-481a-a3c8-1777b5381140/vcredist_x86.exe并安装
```
* k8s docker集群搭建
```
安装集群必要软件—-etcd/flannel/kubernetes:yum -y install –enablerepo=virt7-docker-common-release kubernetes etcd flannel
Etcd服务配置共享和服务发现。
Flannel网络规划服务，让集群不同节点主机创建的Docker容器都具有全集群唯一虚拟IP地址
etcd kube-apiserver kube-controller-manager kube-scheduler flanneld服务跑起来
kube-proxy kubelet flanneld docker起来
设置kupectl配置文件：
kubectl config set-cluster default-cluster --server=http://192.168.121.9:8080
kubectl config set-context default-context --cluster=default-cluster --user=default-admin
kubectl config use-context default-context
查看集群状态：kubectl get nodes
spec:
  replicas: 1 表示起几个pods
kubectl set image
更新现有的资源对象的容器镜像
kubectl apply -f FILENAME
接受JSON和YAML格式的描述文件或控制台输入，对资源进行配置
Helm可以看作k8s的aot-get和yum,软件部署，删除，升级，回滚应用的强大功能
微服务在k8s持续继承部署，借助helm和gitlab-ci完成。
etcd kube-apiserver kube-controller-manager kube-scheduler flanneld服务
```
* go make new
```
new返回类型指针，make返回引用
go switch不用break,但是可以使用fallthrough强制执行后面代码,紧跟着的case
go 变参就是一个slice类型
copy是新的地址
指针传值也是copy，copy的指针地址，可以修改结构体，免去较多系统开销
type testInt func(int) bool函数类型（同样参数同样返回值）
panic() recover()可以从panic恢复，在defer里申明
main和init，没有参数和返回值
一个包被多个包导入，只会被导入一次
点操作：省略包名
别名操作：import f fmt
_操作：不直接使用包里函数，而是调用该包init
面向对象method,带有接收者函数func(r ReceiverType) funcName(parameters)(results)
reflect结合interface使用，reflect.ValueOf()
reflect.TypeOf(),修改值，必须传引用，否则panic: reflect: reflect.flag.mustBeAssignable using unaddressable value
v,ok:= <-ch ok false 无数据关闭
```
* ebpf 使用
```
yum install bcc-tools.x86_64
/usr/share/bcc/tools
./trace 'p:c:open (STRCMP("/etc/passwd", arg1)) "opening %s", arg1' -T
./trace 'p:c:open (STRCMP("/etc/passwd", arg1)) "opening %s %d %d", arg1,$task->pid, $task->parent->pid' -T打
印出父进程PID，需要查看内核task结构体
```
* valgrind检查内存泄漏
* token
```
用户登陆后返回给客户端保存在window.localstorage,每次请求带上这个token
token产生jwt,用户名,过期时间，admin状态encode产生。
之后jwt.decode，验证时间是否过期，用户名是否对
客户端请求路由验证：basic auth,jwt auth,admin status auth
```
* basic auth
```
usrname:passwd  base64传给服务段，服务端base.decode验证
```
* linux proxy
```
wget https://github.com/boypt/vmess2json/raw/master/vmess2json.py
python3 vmess2json.py   --subscribe 'subs' -o 1.json
docker run -d --privileged=true   -v /root/proxychains/vmess2json/1.json:/etc/v2ray/config.json --name v2ray --network host --restart always v2ray/official
docker run  --privileged --name redsocks -e ip=x -d --network host --restart always rbbb/redsocks
git clone https://github.com/rofl0r/proxychains-ng
cd proxychains-ng
./configure --prefix=/usr --sysconfdir=/etc
make 
make install
make install-config
cd .. && rm -rf proxychains-ng
dynamic_chain
chain_len = 1 #round_robin_chain和random_chain使用
proxy_dns 
remote_dns_subnet 224
tcp_read_time_out 15000
tcp_connect_time_out 8000
[ProxyList]
socks5  127.0.0.1 1080
proxychains4 curl 
export  https_proxy=https://127.0.0.1:1080
export  http_proxy=http://127.0.0.1:1080
```
* go build
```
export GOPATH=`cd ../../../;pwd`;CC=arm-linux-gnueabihf-gcc GOOS=linux GOARCH=arm CGO_ENABLED=1 go build -ldflags '-linkmode "external" -extldflags "-static"' -i  *.go
go是运行环境和运行程序一起打包，linkmode参数给c的代码用，静态编译进go可执行文件
```
* beego
```

```
* django
```
bulk_create()批量创建对象，减少SQL查询次数,优化性能
transaction.atomic事务回滚
```
