---
layout:     post
title:      k8s入门实践
subtitle:   
date:       2019-08-26
author:     Hao
catalog: true
tags:
    - k8s
---
#### Installing kubeadm on your hosts
##### 安装
1.设置源
```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF

阿里云镜像

cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

```
2. 安装
```
yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

node节点
```
3. 初始化设置，镜像下载
```
#关闭swap
swapoff -a
#查看需要的镜像列表
kubeadm config images list
#从其他仓库下载镜像
kubeadm config images list |sed -e 's/^/docker pull /g' -e 's#k8s.gcr.io#aiotceo#g' |sh -x  
#重命名镜像 
docker images |grep aiotceo |awk '{print "docker tag ",$1":"$2,$1":"$2}' |sed -e 's#aiotceo#k8s.gcr.io#2' |sh -x  
#删除aiotceo镜像
docker images |grep aiotceo |awk '{print "docker rmi ", $1":"$2}' |sh -x 删除aiotceo镜像

#coredns
docker pull coredns/coredns:1.3.1
docker tag coredns/coredns:1.3.1 k8s.gcr.io/coredns:1.3.1
docker rmi coredns/coredns:1.3.1
```
4.init初始化

```
kubeadm init 
--pod-network-cidr 10.244.0.0/16

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
  
#加入节点  
kubeadm join 10.129.235.56:6443 --token 0cnoqm.2duphcvypsyhm6dg \
    --discovery-token-ca-cert-hash sha256:d5260133bce9db6ee190f09702976f4c7ae6c4d48c460076579f2c832468bf41
    
home: 


kubeadm join 192.168.101.111:6443 --token ra8sk1.zcn897s5br0yih2x \
    --discovery-token-ca-cert-hash sha256:cf0f95dc4d0ba9a9fb460bfbf99d120a3bda743a036ab75d1e4d613e4be66ed0
```


5. 网络插件

```
kubectl apply -f https://git.io/weave-kube-1.6

kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/2140ac876ef134e0ed5af15c65e414cf26827915/Documentation/kube-flannel.yml
quay.io/coreos/flannel:v0.11.0-amd64 to
quay-mirror.qiniu.com/coreos/flannel:v0.11.0-amd64

```
##### 节点管理
```
#查看节点
kubectl get nodes
#加入节点
kubeadm join
#删除节点
kubectl delete node {name}
#重置
kubeadm reset
# 查看sys node info
kubectl describe pod -n kube-system|less
#查看全部pod
kubectl get pods --all-namespaces
# 查看pod列表
kubectl get pods -l  app=hello-server
# 查看pod
kubectl describe pod

```
###### deploy
```
 kubectl replace -f hello-server.yml
 
 kubectl apply -f hello-server.yml
 
 #删除
 kubectl delete -f hello-server.yml
```
##### 私服拉取镜像
```
#创建认证信息
 kubectl create secret docker-registry regsecret --docker-server=10.129.7.131:18083 --docker-username=dev --docker-password=dev --docker-email=xionghao@beisen.com
#查看认证信息
kubectl get secret regsecret --output=yaml
```
##### application配置
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-deployment
spec:
  selector:
    matchLabels:
      app: hello-server
  replicas: 2 # 副本数
  template:
    metadata:
      labels:
        app: hello-server
    spec:
      containers:
      - name: hello-server
        image: 10.129.7.131:18083/go-hello #私有镜像
        ports:
        - containerPort: 8888
      imagePullSecrets:
      - name: regsecret #私服认证信息
```

##### 架构
![组件](https://static001.geekbang.org/resource/image/8e/67/8ee9f2fa987eccb490cfaa91c6484f67.png)

##### pod设计
1. container: 进程
2. pod：进程组
3. Pod 里的所有容器，共享的是同一个 Network Namespace，并且可以声明共享同一个 Volume
4. k8s.gcr.io/pause,基础容器
5. sidecar:在一个 Pod中，启动一个辅助容器，来完成一些独立于主进程（主容器）之外的工作。


##### dashbord
```
#获取配置
wget https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
# 修改NodePort
 vi kubernetes-dashboard.yaml
#拉取镜像
#应用配置
kubectl apply -f kubernetes-dashboard.yaml
#查看pod
 kubectl get pods -A
 #查看端口
 kubectl -n kube-system get svc
 #查看secret
 kubectl -n kube-system get secret
 #查看token
  kubectl -n kube-system describe secrets namespace-controller-token-kzzrl
  #使用token登录
  ip:port
```

##### controller编排
###### replicateSet
1. 水平扩展
2. 滚动更新
3. deployment-->replicatSet-->pod
4. 创建新的replicateSet，添加新的pod，移除旧的pod，滚动更新
###### StatefulSet拓扑状态
1. 拓扑状态：A B容器顺序
2. 存储状态：
###### DaemonSet
###### batch job,CronJob

##### service
```
###hello-service
apiVersion: v1
kind: Service
metadata:
  name: hello-service
spec:
  selector:
    app: hello-service
  ports:
  - name: default
    protocol: TCP
    port: 8888
    targetPort: 8888
    
###
hello-hostnames
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-service
spec:
  selector:
    matchLabels:
      app: hello-service
  replicas: 2
  template:
    metadata:
      labels:
        app: hello-service
    spec:
      containers:
      - name: hello-service
        image: 10.129.7.131:18083/go-hello
        ports:
        - containerPort: 8888
          protocol: TCP
      imagePullSecrets:
      - name: regsecret
```
查看host
```
 kubectl get endpoints hello-service
 
    NAME            ENDPOINTS                       AGE
    hello-service   10.36.0.7:8888,10.44.0.5:8888   2d17h
    
 kubectl get svc hello-service
 
    NAME            TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
    hello-service   ClusterIP   10.98.7.63   <none>        8888/TCP   2d17h
    
curl -i 10.98.7.63:8888/hello    
 ```
 
 #### 网络
 1. 容器网络隔离，通过虚拟交换机连接
 2. 网桥（Bridge）根据 MAC 地址将数据包转发到网桥的不同端口（Port）上。
 3. Veth Pair：虚拟网络设备，桥梁
 

#### ingress
```
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml
kubectl apply -f ingress-nginx-mandatory.yaml

wget  https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/baremetal/service-nodeport.yaml
kubectl apply -f ingress-nginx-service-nodeport.yaml

kubectl get pods --all-namespaces -l app.kubernetes.io/name=ingress-nginx --watch
kubectl describe pod --all-namespaces -l app.kubernetes.io/name=ingress-nginx
#查看version
kubectl exec -it nginx-ingress-controller-7995bd9c47-sskqg -n ingress-nginx  -- /nginx-ingress-controller --version

#查看端口
kubectl get svc -n ingress-nginx

```