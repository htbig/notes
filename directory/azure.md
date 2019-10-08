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
* 准备设备端环境
```
https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux
```
* 编写测试端c sdk的client code，模拟多台设备发送数据到云端，测试设备发向云端的时耗：首次发送100+ms, 之后20+ms.
* 云端到设备端的device method, 测试云端发往设备端的数据到收到返回时间33～55ms.
```
https://github.com/Azure-Samples/azure-iot-samples-python.git (fetch)
```
???
* 获取设备发给云端的消息
```
https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs
```
???
* 云端批量发到设备端大量数据
