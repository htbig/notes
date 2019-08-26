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