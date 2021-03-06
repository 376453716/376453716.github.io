---
layout:     post
title:      Design Data Intensive Applications
subtitle:   
date:       2018-09-06
author:     Hao
header-img: img/jekyll.jpg
catalog: true
tags:
    - bigdata
---

## 可靠性 可扩展性 可维护性

#### 可靠性：保证应用正确的工作
    - 硬件故障：磁盘 内存 电源等故障；硬件冗余
    - 软件错误：服务依赖，基础应用依赖；服务降级 进程隔离 监控告警
    - 人为错误：系统设计，减少犯错的机会；彻底的测试；快速恢复（备份回滚，分批发布）
    
#### 可扩展性：描述系统应对负载增长的能力
    - 负载参数：TPS QPS 数据库读写比率 缓存命中率
    - 人为错误：系统设计，减少犯错的机会；彻底的测试；快速恢复（备份回滚，分批发布）
    - 描述性能：
        - 百分位点：
        - 平均响应时间：将响应时间列表排序，如果第95百分位点响应时间是1.5秒，意味着100个 请求中95个响应快于1.5秒
        - SLO service level objectives 服务等级目标
        - SLA service level agreements：服务等级协议。 可用性 入99.9 低于1秒认为服务正常
        - 排队延迟，头部阻塞，少量缓慢的请求，影响后续本来能很快执行的请求
    - 应对负责：
        - 水平扩展，弹性扩容
        - 无状态服务
        
#### 可维护性       
    - 运维，保证系统平稳运行
    - 简单 消除复杂度
    - 可演化 扩展 修改 可塑性
    - 
     
        
twitter业务方式
1. 直接写入；查询推文时，根据时间线获取关注的人的推文，合并结果
2. 写入时获取关注了的人，插入关注人的缓存中，读取时开销小，写入的数量级比查询低两个数量级,写入缓存开销
3. 混合方式：拥有海量关注着的推文不更新缓存，查询是再和缓存数据合并

