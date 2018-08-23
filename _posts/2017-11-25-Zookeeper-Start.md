---
layout:     post
title:      Zookeeper Start
subtitle:   zk
date:       2017-11-25
author:     Hao
catalog: true
tags:
    - zk
---
```
docker pull daocloud.io/daocloud/zookeeper:latest
# pull
docker pull zookeeper
# run
docker run --name zookeeper_2181 -d zookeeper:latest
docker run  -d --name zookeeper_2181 -p 2181:2181 zookeeper
# log 
docker logs -f zookeeper_2181
```

```
./zkCli.sh -server localhost:2181
```

```
#查看节点
ls /

# -s 代表自动编号
# -e 代表临时节点，当创建这个znode的Client与Server断开连接，此节点自动删除
create [-s] [-e] path data acl

#获取节点数据
get /path

#设置节点数据
set path data [version]


#删除节点
delete path [version]

#查看节点状态
stat path [watch]


```

### cluster
```
1.在conf下创建zoo1.cfg，zoo2.cfg，zoo3.cfg
2.在tmp下创建zoo1/myid，zoo2/myid，zoo3/myid

3.config：
dataDir=tmp/zoo1
server.1=localhost:2888:3888
server.2=localhost:2889:3889
server.3=localhost:2890:3890

4.启动
./bin/zkServer.sh  start conf/zoo1.cfg
./bin/zkServer.sh  start conf/zoo2.cfg
./bin/zkServer.sh  start conf/zoo3.cfg

查看节点状态
./bin/zkServer.sh status conf/zoo3.cfg
leader/follower
```

```
#start all
zkPath=/Users/xionghao/Work/tools/zookeeper-3.4.10
$zkPath/bin/zkServer.sh start $zkPath/conf/zoo2.cfg
echo "start 2"
$zkPath/bin/zkServer.sh start $zkPath/conf/zoo3.cfg
echo "start 3"
$zkPath/bin/zkServer.sh start $zkPath/conf/zoo4.cfg
echo "start 4"


echo `head $zkPath/tmp/zoo2/zookeeper_server.pid`
echo `head $zkPath/tmp/zoo3/zookeeper_server.pid`
echo `head $zkPath/tmp/zoo4/zookeeper_server.pid`
```

```
stop all
zkPath=/Users/xionghao/Work/tools/zookeeper-3.4.10
$zkPath/bin/zkServer.sh stop $zkPath/conf/zoo2.cfg
echo "stop 2"
$zkPath/bin/zkServer.sh stop $zkPath/conf/zoo3.cfg
echo "stop 3"
$zkPath/bin/zkServer.sh stop $zkPath/conf/zoo4.cfg
echo "stop 4"

echo `head $zkPath/tmp/zoo2/zookeeper_server.pid`
echo `head $zkPath/tmp/zoo3/zookeeper_server.pid`
echo `head $zkPath/tmp/zoo4/zookeeper_server.pid`
```