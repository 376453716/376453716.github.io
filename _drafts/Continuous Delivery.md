

#### namespace
1. pid: PID 为 1，又叫超级进程，也叫根进程。它负责产生其他所有用户进程。所有的进程都会被挂在这个进程下，如果这个进程退出了，那么所有的进程都被 kill 。如果一个子进程的父进程退了，那么这个子进程会被挂到 PID 1 下面。
```
pstree -p
```


#### cgroups
```shell
-- 查看系统支持的控制
less /proc/cgroups
-- cgroup控制入口
cd /sys/fs/cgroup
cd cpu
mkdir container_test
while : ; do : ; done &
top |grep 14162
echo 100000 > cpu.cfs_period_us
echo 20000 > cpu.cfs_quota_us
echo 14162 > tasks
cat /proc/14162/cgroup
```
 
## docker
####
标准化：镜像格式，操作，镜像包含了服务运行的环境
隔离性：容器隔离，
轻量级：资源利用率


#### image
docker build
docker inspect 9fe4cdc1f173
ls /var/lib/docker/overlay2/ad5fa5cdc69038277ee0038969d39c4cb48408485f1dffbf8d1d09e661622bc0/diff

#### container
docker run -p 8888:8888 go-hello
docker exec -it  f071491256b5 bash
docker inspect d6cd5617c273|grep id
 ls -l /proc/1920/ns
 ls -l /proc/15350/ns/
### 编排
#### compose
docker
#### k8s

jenkins 
nexus
nginx
supervisord
walle


