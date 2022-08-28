# ERP系统
```
ERP系统和电子标签对接程序编写：
curl -X POST \
  http://xxxxx:8001/api/v1/post_common \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 431adb81-557c-5664-9470-f95d883dbb51' \
  -d '{
    "x-access-token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiSFowODgwOCJ9.hpuBiupMSHaEu9LKOOms8PknZ1cXqxi1uBVoMMMiAIQ",
    "command": "query_all_commodity"
}‘
'得到ERP系统所有商品信息：
{
    "command": "query_all_commodity",
    "output": [
        {
            "barcode": "6928804011142",
            "name": "可口可乐",
            "price": "2.50"
        },
        {
            "barcode": "6953392501010",
            "name": "可口可乐",
            "price": "2.50"
        }
    ],
    "nums": "349",
    "result": "success",
    "message": "success"
}
将这些json数据变为字典，然后组装为列表（二维数组），在json发送到els接口添加数据到els的数据库。
注意事项：
1：esl（Electronic Shelf Label，简称ESL，电子货架标签的英文简称）需要知道所有商品信息，否则数据不符合要求，无法操作。
2：我们ERP系统获取到的商品信息只有3个属性


esl管理店内商品通过vpn实现在同一个域网内，打开vpn在10号设备：
echo net.ipv4.ip_forward=1>/etc/sysctl.conf
iptables -t nat -I  POSTROUTING 1 -s 192.168.3.0/24 -o Management1 -j MASQUERADE
docker run --privileged -d --name="anycntvpn" --net="host" -v /dev/net/:/dev/net/ -v /vbg/root/anycntvpn/etc/ocserv/:/etc/ocserv/ anycntvpn
这样veg 上vpn服务就可以用了，ip也可以转发，装有esl server的机器就可以链接到vpn上跟网关基站交互，但是这一步只是esl server可以ping通网关，网关基站还不能发数据到esl server，需要在openwrt上添加一条路由，目的地是192.168.3.0的都转发到192.168.166.100上，让他到了host上再去转发出去到esl server
显示：
iptables -t nat -vxnL POSTROUTING
删除：
iptables -t nat -D POSTROUTING 1
超市10号设备：172.21.20.162
在openwrt上配一条目的地址192.168.3.0的路由，这样基站网关才能知道如何到els server：
root@OpenWrt:~# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         172.21.76.1     0.0.0.0         UG    0      0        0 eth1
172.21.76.0     *               255.255.252.0   U     0      0        0 eth1
172.21.76.1     *               255.255.255.255 UH    0      0        0 eth1
192.168.3.0     vbg-m8105.lan   255.255.255.0   UG    3      0        0 br-lan
192.168.166.0   *               255.255.255.0   U     0      0        0 br-lan
这样基站过来的包就可以发到els server上了
在Windows机器上装python：
并且安装python的requests包，连接到vpn执行python脚本，ok，都可以同步上

systemd[1]: musicserverd.service: Main process exited, code=exited, status=52
 vega-shell -c HISTFILE=;SA_OS_TYPE="Linux" REAL_OS_NAME=`uname` if [ "$REAL_OS_NAME" != "$SA_OS_TYPE" ] ; then ech
tcpdump -i vpns0 icmp
查看为什么vpn不通
docker-proxy
docker 的端口转发工具. 实现容器与主机的端口映射
mErp:
curl -X POST \
  http://172.21.30.148:8001/api/v1/post_common/ \
  -H 'authorization: Basic YWRtaW46TmV0UmluZzc4OTAw' \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: e29af395-0582-84e8-eb93-525c3b5c045e' \
  -d '{
  "command": "query_dev_sku",
  "dev_id": 2008
}'

rrpc:
curl -X POST \
  http://xxxxxx:8085/rrpc \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -H 'postman-token: 719eb4b6-9778-d0c9-a342-338471259fbc' \
  -d '{
    "devname": "02c000812013583d",
    "req": {
        "A": 102,
        "P": {
        }

    }
}
```
