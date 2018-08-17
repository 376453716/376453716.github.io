## 性能测试

#### http_load
- 可以并发的运行多个http请求，测试web服务器吞吐量
- 运行在单进程中
- 不支持post

参数
-parallel简写-p：并发的用户进程数。 
-fetches简写-f：总计的访问次数 
-rate简写-r：每秒的访问频率 
-seconds简写-s：总计的访问时间

```
$ http_load -p 并发访问进程数 -f 访问总数 需要访问的URL文件
$ http_load -r 每秒访问频率 -s 访问时间 需要访问的URL文件
```

执行结果分析

```
100 fetches, 5 max parallel, 1.11526e+07 bytes, in 3.00543 seconds
111526 mean bytes/connection
### QPS fetches/sec : 每秒响应的请求 ###
33.2731 fetches/sec, 3.71082e+06 bytes/sec
### RT msecs/connect ： 连接平均响应时间msecs ###
msecs/connect: 35.8297 mean, 40.431 max, 32.081 min
msecs/first-response: 37.6184 mean, 47.894 max, 33.316 min
98 bad byte counts
HTTP response codes:
  code 200 -- 100
```


### apache ab
- HTTP服务的性能工具
- give an impression of how your current Apache installation performs
 This especially shows you how many requests per second your Apache installation is capable of serving.

```
ab [ -A auth-username:password ] [ -c concurrency ] [ -C cookie-name=value ] [ -d ] [ -e csv-file ] [ -g gnuplot-file ] [ -h ] [ -H custom-header ] [ -i ] [ -k ] [ -n requests ] [ -p POST-file ] [ -P proxy-auth-username:password ] [ -q ] [ -s ] [ -S ] [ -t timelimit ] [ -T content-type ] [ -v verbosity] [ -V ] [ -w ] [ -x <table>-attributes ] [ -X proxy[:port] ] [ -y <tr>-attributes ] [ -z <td>-attributes ] [http://]hostname[:port]/path
```

执行结果分析

```

Concurrency Level:      10
Time taken for tests:   1.687 seconds
Complete requests:      100
Failed requests:        97
   (Connect: 0, Receive: 0, Length: 97, Exceptions: 0)
Write errors:           0
Total transferred:      11246610 bytes
HTML transferred:       11151611 bytes
###每秒请求数
Requests per second:    59.27 [#/sec] (mean)
###平均请求响应时间
Time per request:       168.724 [ms] (mean)
###服务端平均请求处理时间
Time per request:       16.872 [ms] (mean, across all concurrent requests)
###网络传输速度
Transfer rate:          6509.45 [Kbytes/sec] received

###网络连接时间
Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:       32   37   3.0     38      46
Processing:   104  120  12.6    118     166
Waiting:       34   39   3.8     39      54
Total:        138  157  14.0    157     206


###请求时间分布
Percentage of the requests served within a certain time (ms)
  50%    157
  66%    160
  75%    163
  80%    163
  90%    172
  95%    189
  98%    206
  99%    206
 100%    206 (longest request)

```

