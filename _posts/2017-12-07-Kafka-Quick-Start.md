---
layout:     post
title:      Kafka quick start
subtitle:   
date:       2017-12-07
author:     Hao
catalog: true
tags:
    - kafka
---
#### 配置
```
> tar -xzf kafka_2.11-1.1.0.tgz
> cd kafka_2.11-1.1.0

#启动kafka
#!/bin/bash
kafka_path=/Users/xionghao/Work/tools/kafka_2.12-1.0.1
#start zk
nohup $kafka_path/bin/zookeeper-server-start.sh $kafka_path/config/zookeeper.properties >> $kafka_path/logs/zk/zk-nohup.log 2>&1 &
#start kafka
nohup $kafka_path/bin/kafka-server-start.sh $kafka_path/config/server.properties >> $kafka_path/logs/kafka/kafka-nohup.log 2>&1 &
```

####  使用
```
#创建topic
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

#查看topic
bin/kafka-topics.sh --list --zookeeper localhost:2181

#send message
bin/kafka-console-producer.sh --broker-list localhost:9092,localhost:9093,localhost:9094 --topic my-replicated-topic
This is a message

#consume
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic my-replicated-topic --from-beginning
This is a message

#连接集群
bin/kafka-console-producer.sh --broker-list localhost:9092,localhost:9093,localhost:9094 --topic my-replicated-topic
```

#### Cluster
```
cp config/server.properties config/server-1.properties
cp config/server.properties config/server-2.properties

config/server-1.properties:
    broker.id=1
    listeners=PLAINTEXT://:9093
    log.dir=/tmp/kafka-logs-1
 
config/server-2.properties:
    broker.id=2
    listeners=PLAINTEXT://:9094
    log.dir=/tmp/kafka-logs-2
    
> bin/kafka-server-start.sh config/server-1.properties &
...
> bin/kafka-server-start.sh config/server-2.properties &    

```

#### 启动
```bash
#!/bin/bash
kafka_path=/Users/xionghao/Work/tools/kafka_2.12-1.0.1
#start zk
#nohup $kafka_path/bin/zookeeper-server-start.sh $kafka_path/config/zookeeper.properties >> $kafka_path/logs/zk/zk-nohup.log 2>&1 &
#echo "start zookeeper..."
#sleep 5
#start kafka
nohup $kafka_path/bin/kafka-server-start.sh $kafka_path/config/server.properties >> $kafka_path/logs/kafka/kafka-nohup.log 2>&1 &
echo "start kafka1..."
nohup $kafka_path/bin/kafka-server-start.sh $kafka_path/config/server-1.properties >> $kafka_path/logs/kafka/kafka-server1-nohup.log 2>&1 &
echo "start kafka2..."
nohup $kafka_path/bin/kafka-server-start.sh $kafka_path/config/server-2.properties >> $kafka_path/logs/kafka/kafka-server2-nohup.log 2>&1 &
echo "start kafka3..."
```


