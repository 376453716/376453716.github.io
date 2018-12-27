####
1. 全局锁
1. 表锁
  - lock table read/write：
  - MDL read/write：访问表时自动加锁，防止访问表示表结构被修改
1. 行锁
    - InnoDB行锁在事务结束时释放
1. 死锁
  - 场景
    - 事务A：update t set v=v+1 where id = 1
    - 事务B：update t set v=v+1 where id = 2
    - 事务A：update t set v=v+1 where id = 2
    - 事务B：update t set v=v+1 where id = 1
  - 策略
    - 等待直到超时(默认50s)
    - 死锁检测，主动回滚，耗费cpu资源
