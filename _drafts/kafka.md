#### 概念
- 消息
  - kafka的数据单元，表示一条记录
- 主题
  - 通过主题对消息进行分类
- 分区
  主题可以分为多个分区进行存储
- 生产者
  - 发布消息到特定的主题上
- 消费者
  - 读取订阅的消息
- 消费者组群
  - 多个消费者组成消费者组群，每个消费者消费部分分区的消息
- 偏移量
  - 消费者读取消息的位置
- broker
  - 独立的kafka服务器
- 集群
  - 多个broker组成集群，其中一个为首领

#### kafka特点
- 多生产者，多消费者
- 基于磁盘存储数据
- 伸缩性。容错性
- 高性能

#### 使用场景
- 消息传递
- 日志
- 流处理
- 时间跟踪

#### 消息发送
- 流程
  - 创建消息->指定key、分区->序列化-》分区器决定分区->创建批次->批次发送到broker->返回写入结果与偏移量->写入错误重试
  - 同步发送
  - 异步发送、回调
  - 默认分区：根据key散列映射到特定分区
  - 自定义分区

#### 消息接收
- 消费者组群订阅同一个主题
  - 消费者数量超过分区数量会被闲置
  - 新的消费者加入会发生分区重分配
  - 消费者通过心跳维持与组群的关系和对分区的所有权关系
- 偏移量提交
  - 消费者向_comsumer_offset发送偏移量消息，消费者重启或真分区重新分配后，读取分区的偏移量，然后开始消费
  - 自动提交:提交接收到的最大偏移量
  - commitSync: 同步提交当前偏移量，阻塞，失败会重试
  - commitAsync： 异步提交，失败不会重试，否则可能覆盖其他线程新的偏移量
  - 通过seek从指定位置开始消费

#### 深入
- 集群
    - 通过zk维护集群成员
    - 创建临时节点到zk
    - kafka监听临时节点路径，当broker加入或退出时可以得到通知
- 分区首领选举
  - 使用zk的临时节点选举控制器
- 复制
  - 首领：每个分区有一个首领副本
  - 跟随者副本： 从首领获取消息
  - 同步副本： 持续得到最新消息的副本
  - 首领失效时，只有同步副本可被选举为新首领

#### 数据可靠性
- 复制：
  - 主题分为多个分区，分区是基本的数据块
broker配置
  - 复制系数N，则n-1个分区失效时任然可用，N越高可用性越高，占用磁盘越大
  - 不完全首领选举
    - 场景一：跟随者副本都不可用，只有首领可用，此时首领不可用，跟随者启动，为不同步副本
    - 场景二： 跟随者为不同步副本，此时首领不可用
    - 允许不同步的副本成为首领，会面临消息丢失的风险
    - 不允许则需等待首领恢复，降低了可用性
  - 最小同步副本
    - 设置为2，则需要有两个同步的副本才能写入数据
- 生产者可靠性
  - 需要恰当的acks值
  - 生产者代码错误处理
  - 生产者重试
- 消费者可靠性
  - 偏移量提交

#### 数据管道
- 将kafka作为异构系统的通道

#### 流式处理
- 数据流
  - 无边界数据集的抽象，无限和持续增长
  - 事件流是有序的
  - 不可变的数据记录
  - 事件流可重播
- 流式处理
  - 实时的处理一个或多个事件流
- 处理方式
  - 请求与响应:实时，阻塞
  - 批处理：异步，高延迟，高吞吐量
  - 流处理：异步，低延迟，持续性，非阻塞

#### 硬件选择
- 磁盘：
  - 写入越快，生成消息延迟越低
  - 容量：取决于需要保留的消息数量(1G/day*7)，额外的空间与缓冲区
- 内存：
  - 不需要太大的内存
  - 页面缓存消费者需要读取的消息
- 网络
  - 网络吞吐量决定kafka的数据流量，多消费者导致流入与流出流量不平衡
- cpu
  - 计算能力要求较低，主要进行消息压缩
- broker 数量
  -
