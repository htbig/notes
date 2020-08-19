## 内存泄漏
```
go 内存泄漏：10次内存泄露，有9次是goroutine泄露，
添加代码：package main

import (
    "fmt"
    "net/http"
    _ "net/http/pprof"
)

func main() {
    // 开启pprof，监听请求
    ip := "0.0.0.0:6060"
    if err := http.ListenAndServe(ip, nil); err != nil {
        fmt.Printf("start pprof failed on %s\n", ip)
    }
}
url:ip:port/debug/pprof/
命令行：
go tool pprof http://localhost:6060/debug/pprof/profile   # 30-second CPU profile
go tool pprof http://localhost:6060/debug/pprof/profile?seconds=120     # wait 120s

# 下载heap profile
go tool pprof http://localhost:6060/debug/pprof/heap      # heap profile

# 下载goroutine profile
go tool pprof http://localhost:6060/debug/pprof/goroutine # goroutine profile

# 下载block profile
go tool pprof http://localhost:6060/debug/pprof/block     # goroutine blocking profile

# 下载mutex profile
go tool pprof http://localhost:6060/debug/pprof/mutex
traces看调用栈，然后用list （函数）得到具体堵塞代码，需要源码在本机。

curl http://127.0.0.1:6060/debug/pprof/trace?seconds=20 > trace.out
go tool trace trace.out
go tool pprof http://localhost:1000/debug/pprof/heap
go tool pprof -base ../pprof/pprof.ting.alloc_objects.alloc_space.inuse_objects.inuse_space.009.pb.gz ../pprof/pprof.ting.alloc_objects.alloc_space.inuse_objects.inuse_space.008.pb.gz
```
## 测试
### 测试用例
```
测试文件必须_test.go结尾，
import testing
测试格式：func TestXxx (t *testing.T)
testing.T的Error, Errorf, FailNow, Fatal, FatalIf方法，说明测试不通过，调用Log方法用来记录测试的信息
go test -v 显示详情
```
### 压力测试
```
格式：func BenchmarkXXX(b *testing.B) { ... }
go test不会默认执行压力测试的函数,go test -bench=.
go test -test.bench=".*" -count=5指定执行多少次
go test -run=文件名字 -bench=bench名字 -cpuprofile=生产的cprofile文件名称 文件夹
```
## GOSSAFUNC
```
GOSSAFUNC=Foo go build *.go可以生成ssa.html查看Foo编译情况
```
## 读写锁
 ```
 分为4个函数
 RLOCK   ----->readcount原子+1，如何加了1，还小于0，说明有人在写，就等readerSem，直到writer写完。。。
 RUNLOCK ----->readcount原子-1，如何结果小于0，说明有人想写，readerWait-1，释放writerSem
 LOCK    ----->先获取写互斥锁，如果有人读，就将readerWait加上正在读的个数，然后quire writerSem，等地所有读结束。
 UNLOCK  ----->先释放所有的读等待readerSem，这样RLOCK就可以获取成功了。然后释放互斥锁
 ```
## go 包的管理
```
go modules,go 1.11之前没有go mod
1: mkdir -p /root/go/test
2: cd /root/go/test
3: go mod init test会在目录下生成一个go.mod文件
4: 编辑一个go文件
5: go run *.go会将go文件所依赖的包下载到GOPATH目录下的pkg/mod下。
```
## channel
 ```

 ```
 ## schedt
 ```
 有个sched变量统领全局
 ```

 ## 线程切换
 ```
 go 1.14之前是协作式调度，不会向线程那样，时间片到了强行调度走，只会设置抢占标记，之后调度器会处理
 线程在同一cpu上运行，每个线程运行都会用到cpu寄存器，但每个cpu只有一组寄存器，所以操作系统在把线程b调度到cpu上运行时，需要先把正在执行的线程a所用到的寄存器值全部保存在内存中，然后再把保存在内存中线程b的寄存器值全部放回到cpu寄存器，这样线程b接着之前运行状态运行。
 对于调度器，线程至少包含3个重要内容：
 1:一组通用寄存器值
 2:将要执行的下条指令地址（pc）
 3:栈（sp,bp）
 线程本地存储又叫线程局部存储，tls，其实就是线程私有全局变量，利用不同线程fs段基址实现。
 每个g有自己的fs寄存器存着tls[1],这样就找到tls[0],tls[0]存放的是g0，g0的m又指向的是m
 ```
## go 进程启动流程
```
首先内核进入用户态，通用入口汇编语言，_rt0_amd64_plan9-->rt0_go(asm_amd64.s),经M0和G0绑定，g0的栈空间为sp--sp-64k+104大小。一些初始化：runtime·args(SB) runtime·osinit(SB)（得到CPU核数） runtime·schedinit(SB)（栈，内存分配器，创建p等）设置最大的m数为10000，,newproc来运行主goroutine的runtime.main，其中newproc的函数为main goroutine,参数size为0，新建的goroutine放到当前_p_的local队列里，启动m0，开始进入调度schedule,启动一个监控任务来抢占执行时间过长的G，以及从epoll获取可以执行的g
``` 
## sysmon
```
always run without p,
```
## newproc
```
new g is _Gdead status 
```
## startm
```
汇编里runtime·mstart，就是启动M0，
```
## systemstack
```
会切换到g0栈执行
```
 ## procresize
 ```
 全局变量
 allp
 allpLock 
最开始在runtime·schedinit被调用的时候，p数量是0，所有的p全是创建的,所以返回的runnable p应该也是空nil，allp是一个存放p的slice，p默认被创建ncpus个，当然也要检查GOMAXPROCS环境变量，make nprocs个p,然后让allp指向它，初始化p,初始状态为_Pgcstop，每个p都有一个id，如果是让p数量变小，那就将不用的p释放，如果该p上有goroutine就将他们push到globle queue的头部去，p.runnext有的话也放到globle queue的头部，有background worker也放到globle queue,p状态改为_Pdead,查看当前的g运行在哪个p上，如果pid小于nprocs，则继续用这个p，否则需要释放当前的p,然后绑定到allp[0],然后将m0绑定到allp[0].状态设置为_Pidle，接着遍历所有的p,状态设置为_Pidle，如果它上面没有运行的g，就将它放到idle里，pidleput()，这个时候出了allp[0]的p全部放到空闲队列。否则就放到runnable的队列里，返回。最后将nprocs放到gomaxprocs变量里。
在最初的阶段，会将m0和allp[0]绑定，绑定完后将p状态变为_Prunning。最初runnablePs为nil，除了与m0绑定的p，其他所有p均为_Pidle状态
 ```
 ## execute
 ```
 先将g状态变为_Grunning从_Grunnable，让gogo汇编代码会执行，gp.sched就是gobuf存着go的各种sp, pc，gogo汇编里pc执行goexit处，然后会跳到函数代码处去执行，所以在函数执行完会到goexit，然后重新进入schedule调用，
 ```
 ## 正常退出goroutine
 ## mcall
 ```
 switch from g to g0 stack and invokes the fn,the g is the goroutine that made the call,h执行goexit0，首先把g状态有_Grunning变为_Gdead，清掉变量g的内容，解绑定m和g的关系，将g放到p本地队列：gfput，进入schedule（），这个是正常执行一个g,并且正常退出的情况
 _g_.m.curg.m = nil
 _g_.m.curg = nil
 ```
## 主动让出cpu
```
 time.Sleep、IO 阻塞等会主动让出cpu，
```
## 找到能运行的goroutine（findrunnable）
```
获取当前的__g__, __p__,首先runqget(_p_)找执行的g，没有得到就会去判断sched.runqsize是否不为0，是就从globrunqget(_p_, 0)获取g，globle queue是需要加锁的。从globle queue拿g的话也是批量拿globle queue总量平均到每个p上的个数那么多，并且这个数量还需要小于当前__p__的一半，然后将第一个g返回，其他g放到__p__队列中去，没有接着netpoll找可执行g
```
## gogo
```
gogo汇编实现g0到g(即将运行的goroutine)的切换,参数为gp.sched，gobuf类型，存放着gp的sp，pc,bp等信息，完成：
1:g放到tls[0],sp,ret，bp等值从内存里load到寄存器，清0这些内存，将pc放到BX寄存器，然后jmp到BX执行，也就是到gp的func处执行，在系统最开始的时候就是条到runtime.main处执行
```
## 查看goos/goarch
```
go tool dist list
GOOS指的是目标操作系统
GOARCH指的是目标处理器的架构
```
