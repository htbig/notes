2008  apt-get install     apt-transport-https     ca-certificates     curl     gnupg-agent     software-properties-common
 2009  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
 2010  apt-key fingerprint 0EBFCD88
 2011  add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
 2012  apt-get update
 2013  apt install kubernetes etcd
 2014  apt install  etcd
 2015  apt search kubernetes
 2016  apt install kubernetes
 2017  apt search k8s
 2018  apt search kuber
 2019  apt search kuber|grep kube
 2020  hostnamectl set-hostname  k8s-nodex
 2021  cat /etc/hostname
 2022  vim /etc/hostname
 2023  snap install kubectl --classi
 2024  snap install kubectl
 2025  snap install kubectl --classic
 2026  apt-get update && apt-get install -y apt-transport-https
 2027  curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -
 2028  cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF


 2029  cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF

 2030  cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF

 2031  apt-get update
 2032  apt-get install -y kubectl kubelet kubeadm
 2033  kubeadm -v
 2034  kubeadm
 2035  kubeadm --version
 2036  kubeadm --v=5
 2037  kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.14.2 --pod-network-cidr=10.244.0.0/16
 2038  kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.19.0 --pod-network-cidr=10.244.0.0/16
 2039  systemctl enable docker.service
 2040  kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.19.0 --pod-network-cidr=10.244.0.0/16
 2041  kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.20.0 --pod-network-cidr=10.244.0.0/16
 2042  netstat -ano|grep 2379
 2043  netstat -anop|grep 2379
 2044  netstat -anop|grep 2380
 2045  systemctl status etcd
 2046  kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.20.0 --pod-network-cidr=10.244.0.0/16
 2047  kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.20.0 --pod-network-cidr=10.244.0.0/16 --ignore-preflight-errors=...
 2048  systemctl stop etcd
 2049  systemctl status etcd
 2050  kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.20.0 --pod-network-cidr=10.244.0.0/16 --ignore-preflight-errors=...
 2051  cd /var/lib/etcd/
 2052  ;s
 2053  ls
 2054  cd default/
 2055  ls
 2056  cd member/
 2057  ls
 2058  cd ../../
 2059  ls
 2060  pwd
 2061  ls
 2062  rm -rf default/
 2063  kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.20.0 --pod-network-cidr=10.244.0.0/16 --ignore-preflight-errors=...
 2064  su ht
 2065  docker images
