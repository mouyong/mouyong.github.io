# 【实践记录】laravel-octane-workerman ab 压测结果

文章编写于 2022年05月02日

## 背景

压测 dcat-admin 管理后台，tenant 租户页面并发量。

测试时间：`2022-05-22 22:50`

## 第一次测试

HttpWorker `count=20`

`ab -n 1000 -c 100 http://foo.xk.hecs.iwnweb.com/`

```shell
ab -n 1000 -c 100 http://foo.xk.hecs.iwnweb.com/
This is ApacheBench, Version 2.3 <$Revision: 1843412 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking foo.xk.hecs.iwnweb.com (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        nginx
Server Hostname:        foo.xk.hecs.iwnweb.com
Server Port:            80

Document Path:          /
Document Length:        74 bytes

Concurrency Level:      100
Time taken for tests:   49.387 seconds
Complete requests:      1000
Failed requests:        21
   (Connect: 0, Receive: 0, Length: 21, Exceptions: 0)
Non-2xx responses:      21
Total transferred:      20145992 bytes
HTML transferred:       19095422 bytes
Requests per second:    20.25 [#/sec] (mean)
Time per request:       4938.698 [ms] (mean)
Time per request:       49.387 [ms] (mean, across all concurrent requests)
Transfer rate:          398.36 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   0.9      0       6
Processing:   368 4166 2979.8   3550   15704
Waiting:      356 4166 2979.8   3550   15704
Total:        369 4167 2979.8   3550   15705
WARNING: The median and mean for the initial connection time are not within a normal deviation
        These results are probably not that reliable.

Percentage of the requests served within a certain time (ms)
  50%   3550
  66%   5036
  75%   5942
  80%   6640
  90%   8146
  95%   9745
  98%  11529
  99%  14121
 100%  15705 (longest request)
```

## 第二次测试

HttpWorker `count=20`
`ab -n 10000 -c 1000 http://foo.xk.hecs.iwnweb.com/`

```shell
ab -n 10000 -c 1000 http://foo.xk.hecs.iwnweb.com/
This is ApacheBench, Version 2.3 <$Revision: 1843412 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking foo.xk.hecs.iwnweb.com (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:        nginx
Server Hostname:        foo.xk.hecs.iwnweb.com
Server Port:            80

Document Path:          /
Document Length:        74 bytes

Concurrency Level:      1000
Time taken for tests:   318.478 seconds
Complete requests:      10000
Failed requests:        4988
   (Connect: 0, Receive: 0, Length: 4988, Exceptions: 0)
Non-2xx responses:      4988
Total transferred:      196737924 bytes
HTML transferred:       190639184 bytes
Requests per second:    31.40 [#/sec] (mean)
Time per request:       31847.833 [ms] (mean)
Time per request:       31.848 [ms] (mean, across all concurrent requests)
Transfer rate:          603.27 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0  114 319.6      0    1034
Processing:    12 26533 18287.0  23213   60432
Waiting:       12 26533 18287.0  23211   60432
Total:         13 26647 18300.8  23368   61036

Percentage of the requests served within a certain time (ms)
  50%  23368
  66%  35615
  75%  41811
  80%  45313
  90%  53993
  95%  60001
  98%  60005
  99%  60008
 100%  61036 (longest request)
```