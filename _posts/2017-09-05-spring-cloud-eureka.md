---
layout:     post
title:      eureka
subtitle:   Spring Cloud Eureka
date:       2017-09-05
author:     Hao
catalog: true
tags:
    - spring,eureka
---
# eureka

服务治理，通过服务注册与服务发现来完成对微服务应用实例的自动化管理

### 架构

1.服务注册中心
2.服务提供者
3.服务消费者

![Eureka architecture](img/spring-cloud/Eureka.png)

### 服务治理机制

###### 服务提供者

服务注册
> 服务提供者启动后发送rest请求到注册中心
多个服务注册中心之间同步服务

服务续约
> 服务提供者维护心跳Renew,防止定时剔除任务将其移出服务列表
```
instance:
    #过期时间
    lease-expiration-duration-in-seconds: 90 
    #服务续约任务间隔时间
    lease-renewal-interval-in-seconds: 30
```

###### 服务消费者

获取服务
> 发送REST请求到服务注册中心获取服务清单
```
eureka:
  client:
    ###获取服务注册间隔时间
    registry-fetch-interval-seconds: 
```

服务调用
>  从服务清单中,通过服务名获取服务实例的信息,然后客户端根据需要决定调用哪个实例
Region包含多个zone

服务下线
> 发送REST请求,注册中心修改服务状态,传播下线事件

###### 服务注册中心

失效服务移除
> 定时任务,移除超时未续约的服务

自我保护
> 
```
EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE.
# 关闭保护机制
eureka.server.enable-self-preservation=false
```
服务中心统计心跳失败比例 15分钟内低于85%, 不过期
