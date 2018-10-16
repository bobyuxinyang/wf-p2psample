# wf-p2psample
a p2p app sample

# 开始

## 运行调度服务

```bash
./xpfsd

```

## 运行客户端服务

在同一机器或者不同机器运行均可。同一机器可以开多个客户端测试。

```
./client --confAddr 127.0.0.1:10000 --storageDir dir2
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:	export GIN_MODE=release
 - using code:	gin.SetMode(gin.ReleaseMode)

[GIN-debug] POST   /save                     --> main.saveFileHandler (5 handlers)
[GIN-debug] GET    /load                     --> main.loadFileHandler (5 handlers)
2018-10-16 13:31:56.172 DEBUG:17118:client/client.go:86: bind to tcp port: 16183
[GIN-debug] Listening and serving HTTP on :16183
2018-10-16 13:31:56.173 INFO:17118:sdk/core.go:29: start connect to conf server localhost:10000
2018-10-16 13:31:56.173 INFO:17118:sdk/core.go:36: conf server connected localhost:10000
2018-10-16 13:31:56.179 DEBUG:17118:sdk/core.go:44: peer_id:13038409973328801452 predict_servers:"assist-001.qitiancloud.com" predict_servers:"assist-002.qitiancloud.com" stun_servers:"assist-001.qitiancloud.com:3478" nat_server:"nat-001.qitiancloud.com:20010"
2018-10-16 13:31:56.18 DEBUG:17118:sdk/nat.go:64: start heartbeat
2018-10-16 13:31:56.217 DEBUG:17118:sdk/udpconn.go:302: bind udp to: 192.168.2.196:59783

2018-10-16 13:32:05.812 DEBUG:17118:sdk/nat.go:77: NAT Type: Symetric NAT
2018-10-16 13:32:10.849 DEBUG:17118:sdk/nat.go:64: start heartbeat
2018-10-16 13:32:16.857 DEBUG:17118:sdk/nat.go:64: start heartbeat
2018-10-16 13:32:20.447 DEBUG:17118:sdk/nat.go:77: NAT Type: Symetric NAT
2018-10-16 13:32:20.448 DEBUG:17118:sdk/nat.go:88: >>>>> public add got <<<<<
```

出现 _>>>>> public add got <<<<<_ 字样表示客户端已就绪

## 客户端上传文件

* 使用 /save 服务
* 文件上载端口，可以在客户端启动输出信息中查找
* 上传完成后返回文件hash
* 文件会被按1MB大小，分片存储到当前其他client

```bash
$ curl --data-binary "test.zip" http://localhost:16183/save
{"hash_id":"d48140a920485af3ffa16449c116fbe2d2d9e9b6"}%
```


## 客户端下载文件

* 使用hash标识下载的文件
* 可以从任何一个客户端下载文件

```
curl -o test.zip http://localhost:53855/load\?file_hash\=e6906814a97ab493c87d0eeaa29cdaf53932f783
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11.6M    0 11.6M    0     0   111M      0 --:--:-- --:--:-- --:--:--  112M
```

## 存在问题

* ipv4网络下支持NAT穿透，但出现Symetric NAT类型，client之间无法直接通信（大约1/3的客户端是Symetric NAT）

