# azure iot install
```
wget https://www.python.org/ftp/python/3.5.6/Python-3.5.6.tgz
tar zxvf Python-3.5.6.tgz
cd Python-3.5.6/
./configure
make -j8
make test
sudo make install
sudo python3.5 -m pip install flask
sudo python3.5 -m pip install flask_restful
 sudo python3.5 -m pip install flask_httpauth

sudo python3.5 -m pip install requests
sudo python3.5 -m pip install azure
sudo python3.5 -m pip install azure-eventhub

/usr/lib/x86_64-linux-gnu/libpython3.5m.so.1.0
/usr/lib/x86_64-linux-gnu/libcurl.so.4

pip3 install --pre azure-eventhub会安装更新版本的eventhub，但是稳定版本还时1.3.1版本
pip3 install azure-iothub-service-client
apt-get install libboost-python-dev
apt install virtualenv
pip3 install virtualenv

virtualenv -p /usr/local/bin/python3.5 py356创建虚拟环境
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev

 sudo apt-get install libssl-dev zlib1g-dev
```
# azure iot 测试
## 1 day
* 准备设备端环境
```
https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux
```
* 编写测试端c sdk的client code，模拟多台设备发送数据到云端，测试设备发向云端的时耗：首次发送100+ms, 之后20+ms.
* 云端到设备端的device method, 测试云端发往设备端的数据到收到返回时间33～55ms.
```
https://github.com/Azure-Samples/azure-iot-samples-python.git (fetch)
```
## 2 day
* 设备到云端单台设备测试发送10000条信息，丢了1条（默认retry policy,不会retry），加上retry没丢
* 设备到云端模拟10000台设备发送数据50万，10万均未丢数据。
## 3day
* 获取设备发给云端的消息
* 采用消息队列方式
```
https://docs.azure.cn/zh-cn/service-bus-messaging/service-bus-python-how-to-use-queues（这两种：消息队列或者消息主题）
https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs（这种方式不太采用，需要自己控制读取消息的位置）
```
* 采用消息队列或者消息主题区别
```
https://docs.azure.cn/zh-cn/service-bus-messaging/service-bus-messaging-overview?toc=https%3A%2F%2Fdocs.azure.cn%2Fzh-cn%2Fservice-bus-messaging%2FTOC.json&bc=https%3A%2F%2Fdocs.azure.cn%2Fzh-cn%2Fbread%2Ftoc.json
```
* 国内一般看https://www.azure.cn/，不看.com
## 4 day
* 云端批量发到设备端大量数据,批量命令，云端到设备端串行下发10000万条信息，正常速度体验
```
for ((i=0; i<1000; ++i))  ; do   curl -X POST http://40.73.247.179:8086/rrpc -H 'Authorization: Basic YXBpOnh4' -H 'Cache-Control: no-cache'  -H 'Content-Type: application/json' -d '{"devname": "test1","req": {"A": 3,"P":10}}' ; done
```
* 流分析
```
https://www.azure.cn/zh-cn/home/features/stream-analytics/
```
* 机器学习
```
https://azure.microsoft.com/zh-cn/services/machine-learning-service/
https://docs.microsoft.com/zh-cn/azure/cognitive-services/custom-vision-service/export-your-model
https://azure.microsoft.com/zh-cn/services/cognitive-services/anomaly-detector/
```
* 查询设备台数
```
SELECT COUNT() AS numberOfDevices FROM c
```
    
* 自定义视觉：提供微软的算法处理图象，中国区不支持打框。不打框识别中国区使用SDK创建app，上传图片，训练模型和测试
```
https://docs.azure.cn/zh-cn/cognitive-services/computer-vision/quickstarts-sdk/python-sdk
* Azure 机器学习服务是一项云服务，可以使用它来训练、部署、自动执行以及管理机器学习模型，所有这些都是在云提供的广泛范围内进行的
```
https://docs.microsoft.com/zh-cn/azure/machine-learning/service/overview-what-is-azure-ml
Azure 机器学习服务提供平台服务，算法可以是用户自己的。

    目前中国区不支持，官网显示年底即将上线GA版。
https://azure.microsoft.com/zh-cn/global-infrastructure/services/?regions=china-non-regional,china-north,china-north-2,china-east,china-east-2&products=machine-learning-service
```

