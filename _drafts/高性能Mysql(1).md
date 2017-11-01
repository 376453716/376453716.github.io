## mysql架构

![Eureka architecture](img/mysql/architecture.jpg)
- 连接管理、授权认证、安全
- 查询解析、分析、优化、缓存、函数
- 存储引擎

#### 并发控制
- Read Lock / Write Lock
- 锁粒度 
    - 表锁: 开销小，
    - 行锁: 支持更多并发，更大的锁开销

#### 事务
- ACID
- 隔离级别
    ![Eureka architecture](img/mysql/islate.png)
- 事务日志： 先更新内存中的数据，再讲变更记录写入事务日志(追加日志，顺序I/O)，再定时写入磁盘

#### MVCC Multiversion Concurrency Control


#### 存储引擎
- MyIsam
    - 全文检索
    - 不支持行级锁、事务
- InnoDB

## 基准测试与性能分析
#### 指标
- 单位时间事务处理量
- 响应时间
- 扩展性
- 并发性   
#### 工具
- ab
- http_load
- JMeter

#### sysbench
```
## cpu
sysbench --test=cpu --cpu-max-prime=20000 run

## I/O,准备测试文件，超过内存限制，避免操作系统缓存
sysbench --test=fileio --file-total-size=20G prepare

##随机读写基准测试
sysbench --test=fileio --file-total-size=1G --file-test-mode=rndrw --init-rnd=on --max-time=300 --max-requests=0 run

## oltp 事务测试
#### 准备数据
 

#### 只读模式
sysbench --test=oltp --oltp-table-size=1000000 --mysql-db=test --mysql-user=root --max-time=60 --oltp-read-only=on --max-requests=0 --num-threads=8 prepare
```

#### profile
```
### 打开统计
set profiling=1;
### 显示已分析过的查询
show profiles;
### 显示指定查询的分析
show profile for query 5;

```