## install and run
```
$ wget http://download.redis.io/releases/redis-2.8.17.tar.gz
$ tar xzf redis-2.8.17.tar.gz
$ cd redis-2.8.17
$ make
$ cd src
$ ./redis-server
```
## cache
```
单值缓存
SET key value
GET key
对象缓存
1）SET user:1 value(json格式)
2）MSET user:1:name ht user:1:balance 1111
   MGET user1:name user:1:balance
   M操作适合修改结构体某个字段频繁操作
   哈希
    HMSET user 1:name ht 1:balance 111
    HMGET user 1:name 1:balance
分布式锁
SETNX product:10001 true
SETNX product:10001 false
DEL product:10001
计数器
INCR key
GET key  阅读量
WEB集群session共享
spring session+redis实现session共享
分布式系统全局序列号
INCRBY orderid 1000
SET
抽奖
SADD act:101 11111
SADD act:101 11112
SADD act:101 11113
SMEMBERS act:101 查看池子里元素
SRANDMEMBER act:101 2随机抽两个元素，不删元素
SPOP act:101 1随机抽取一个元素，且删除元素

## redis单线程模型
```
%3CmxGraphModel%3E%3Croot%3E%3CmxCell%20id%3D%220%22%2F%3E%3CmxCell%20id%3D%221%22%20parent%3D%220%22%2F%3E%3CmxCell%20id%3D%222%22%20value%3D%22%E8%AE%BE%E7%BD%AEkey%20value%E5%88%B0%E5%86%85%E5%AD%98%EF%BC%8C%E5%B0%86AE_WRITEABLE%E4%BA%8B%E4%BB%B6%E4%B8%8E%E5%AE%A2%E6%88%B7%E7%9B%B8%E5%BA%94%E5%A4%84%E7%90%86%E5%99%A8%E7%BB%91%E5%AE%9A%22%20style%3D%22rounded%3D0%3BwhiteSpace%3Dwrap%3Bhtml%3D1%3B%22%20vertex%3D%221%22%20parent%3D%221%22%3E%3CmxGeometry%20x%3D%22790%22%20y%3D%22119%22%20width%3D%2290%22%20height%3D%2280%22%20as%3D%22geometry%22%2F%3E%3C%2FmxCell%3E%3C%2Froot%3E%3C%2FmxGraphModel%3E
```

## 批量删数据
```
redis-cli -a 123456 keys "*1323683688461795328*" | xargs redis-cli -a 123456 del
```
## 查询包含特定字符串key值数量
```
#!/bin/bash

A=$0

B=${A##*/}

C=${B%.*}

running_file_name=$C

running_flag="run.$running_file_name"

#REDIS_CLIENT=? redis-cli -h 10.19.10.59 -p 22141 -a f31a7566aded44b2925e5a53f9a8798f?

echo = "??"

function process {

echo $0

index=-1

count=0

step=100000

while ((index!=0))

do

if [ $index -le 0 ];then

index=0

fi

echo $index

echo scan $index match $1 count $step  | redis-cli -h 127.0.0.1 -p 6379 -a 123456 > $running_file_name.cache

read index <<< `head -1 $running_file_name.cache`

read inc <<< `cat $running_file_name.cache | wc -l`


echo $inc

inc=$(($inc - 1))

if [ $? -ne 0 ];then

break

fi

count=$(($count + $inc))

done

echo "$1 count:"$count

}

#

if [ $# -ne 1 ];then

echo "$0"

exit 0

fi

#

if [ -f "$running_flag" ] ; then

echo "is running?"

exit 0

fi

#

touch $running_flag

#

echo "processing?."

echo $*

process $*

#

rm -rf $running_flag

#

echo "ok!"#

echo "ok!"
```
