## 读写锁
 ```
 分为4个函数
 RLOCK   ----->readcount原子+1，如何加了1，还小于0，说明有人在写，就等readerSem，直到writer写完。。。
 RUNLOCK ----->readcount原子-1，如何结果小于0，说明有人想写，readerWait-1，释放writerSem
 LOCK    ----->先获取写互斥锁，如果有人读，就将readerWait加上正在读的个数，然后quire writerSem，等地所有读结束。
 UNLOCK  ----->先释放所有的读等待readerSem，这样RLOCK就可以获取成功了。然后释放互斥锁
 ```

 ## channel
 ```

 ```