---
layout:     post
title:      spring-cloud(2)
subtitle:   spring cloud robbin
date:       2017-10-03
author:     Hao
# header-img: img/spring-cloud/spring-cloud-robbin.jpg
catalog: true
tags:
    - spring
    - spring cloud
    - ribbon
---
# robbin 
客户端负载均衡

## 负载均衡
用来在多个计算机、网络连接、CPU或其他资源中分配负载，以达到最佳化资源使用、最大化吞吐率、最小化响应时间、同时避免过载的目的。
- 分发请求
- 确保服务可用性与可靠性
- 根据需要动态增减服务

### 服务端负载均衡
服务端负载均衡
- LVS
- NGINX反向代理

### 客户端负载均衡
客户端维护服务清单与负载均衡策略

### RestTemplate
用于客户端HTTP访问的核心类

### 切换HTTP连接库
默认使用JDK HTTP库，可以自定义切换
standard JDK facilities to establish HTTP connections.
Apache HttpComponents, Netty, and OkHttp.
```
setRequestFactory
```


### spring cloud ribbon 自动化配置

```
IClientConfig:
IRule:
IPing:
ServerList:
ServerListFilter:
ILoadBalance:
```

### 源码分析

1. 使用@LoadBalanced标记RestTemplate将使用LoadBalancerClient配置
2. 加载LoadBalanceAutoConfiguration
    ```
    @ConditionalOnClass(RestTemplate.class)
    @ConditionalOnBean(LoadBalancerClient.class)
    @EnableConfigurationProperties(LoadBalancerRetryProperties.class)
    ```
    - 创建LoadBalancerInterceptor，对客户端请求进行拦截
    - 维护需要LoadBalanced的RestTemplate对象列表
    - 创建loadBalancedRestTemplateInitializer，对RestTemplate增加LoadBalancerInterceptor拦截器
3. LoadBalancerInterceptor中注入LoadBalancerClient实现，通过request获取请求的服务Host(serviceName)，调用LoadBalancerClient.execute()执行请求
4. LoadBalancerClient.execute()
    - 根据serverID获取ILoadBalancer，根据负载均衡器loadBalancer.chooseServer()获取服务实例，包装为RibbonServer对象
    - 回调LoadBalancerInterceptor中LoadBalancerRequest.apply(),apply()根据服务实例组装具体的请求地址，发送请求
5. 负载均衡器
    ![ILoadBalancer](https://376453716.github.io/img/spring-cloud/ILoadBalancer.png)
    ```
        public void addServers(List<Server> newServers);
        public Server chooseServer(Object key);
        public void markServerDown(Server server);
        public List<Server> getReachableServers();
        public List<Server> getAllServers();
    ```
    - 服务清单：
        - ![ServerList](https://376453716.github.io/img/spring-cloud/ServerList.png)
        - DomainExtractingServerList: 通过EurekaClient从服务注册中心获取服务实例列表
        - ServerListUpdater: 更新本地服务清单,默认通过定时任务轮训更新，也可根据Eureka的事件监听器驱动服务列表的更新操作
    - 服务清单过滤器：
        - ![ServerListFilter](https://376453716.github.io/img/spring-cloud/ServerListFilter.png)
    - BaseLoadBalancer: 定义allServerList，upServerList，IPing，LoadBalancerStats,SerialPingStrategy,IRule,setupPingTask,addServers
    - DynamicServerListLoadBalancer: 服务清单动态更新，服务清单过滤器
    

6. 负载均衡策略
    ![IRule](https://376453716.github.io/img/spring-cloud/IRule.png)
    - AbstractLoadBalancerRule: 
    - RandomRule: 随机选择
    - RoundRobinRule: 线性轮训，通过AtomicInteger 实现
    - RetryRule: 重试机制
    - WeightedResponseTimeRule: 根据权重分配，通过serverWeightTimer定时任务统计计算服务实例的权重。生成
        - 统计所有实例平均响应时间
        - 计算权重，根据平均响应时间计算每个实例的权重区间，生成权重区间内的随机数，
    - ClientConfigEnabledRoundRobin: 内部定义RoundRobinRule
    - BestAvailableRule: 注入LoadBalancerStats，过滤故障实例选，并发请求数最小的实例
    - AvailabilityFilteringRule: 过滤断路器是否生效，并发请求数最大值，然后线性抽样尝试寻找可用且较空闲的实例
    - ZoneAvoidanceRule: 
