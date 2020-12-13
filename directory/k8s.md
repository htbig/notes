
## 步骤
```
阿里云主机选择centos:7.9-64
yum install kubernetes etcd -y
vim /etc/kubernetes/apiserver
删除KUBE_ADMISSION_CONTROL="--admission-control=NamespaceLifecycle,NamespaceExists,LimitRanger,SecurityContextDeny,ServiceAccount,ResourceQuota"

在master节点,分别启动etcd,kube-apiserver,kube-scheduler

在node节点,启动kubelet服务

kubectl get pods
kubectl get node

举个例子:
在node设备上创建nginx.yaml文件内容如下:
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:alpine
    ports:
    - containerPort: 80

执行:kubectl apply -f nginx.yaml -s masterIP:port

执行kubectl get pods -s masterIP:port可以看到类似:
[root@k8s-test ~]# kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
nginx     1/1       Running   0          16m
如果status 一直是ContainerCreating,可以查看node上的kubelet服务的状态日志,看到错误mage pull failed for registry.access.redhat.com/rhel7/pod-infrastructure:latest, this may be because there are no credentials on this request.  details: (open /etc/docker/certs.d/registry.access.redhat.com/redhat-ca.crt: no such file

解决方案:
wget http://mirror.centos.org/centos/7/os/x86_64/Packages/python-rhsm-certificates-1.19.10-1.el7_4.x86_64.rpm
rpm2cpio python-rhsm-certificates-1.19.10-1.el7_4.x86_64.rpm | cpio -iv --to-stdout ./etc/rhsm/ca/redhat-uep.pem | tee /etc/rhsm/ca/redhat-uep.pem
这样docker pull registry.access.redhat.com/rhel7/pod-infrastructure:latest就可以成功了.
```
