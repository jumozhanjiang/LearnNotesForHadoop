### presto的安装

[服务端下载地址与官方教程](https://prestodb.github.io/docs/current/installation/deployment.html)

* 把下载好的压缩包解压,并在里面创建一个`etc`的文件夹,并配置如下这些文件
  * `node.properties`
    * 每个节点的环境配置信息
  * `jvm.config`
    * JVM的命令行选项
  * `config.properties`
    * Presto server的配置信息
  * `log.properties`
    * 配置日志输出相关
  * `catalog`
    * 为数据源的配置信息文件夹,里面可以配置多个数据源[参考文档](https://prestodb.github.io/docs/current/connector.html)

#### <h3>`node.properties`</h3>

```
node.environment=prestoCloud
node.id=1
node.data-dir=/home/yetao_yang/presto/data
```
* `node.environment`
  * 集群名称, 每个节点上的`node.environment`应保持相同
* `node.id`
  * 节点id, 每个节点上的`node.environment`应保持不同
* `node.data-dir`
  * 数据存储目录的位置,应避免在`presto`安装目录里面,这样可以轻松的升级`presto`

#### <h3>`jvm.config`</h3>

```
-server
-Xmx32G
-XX:+UseConcMarkSweepGC
-XX:+ExplicitGCInvokesConcurrent
-XX:+CMSClassUnloadingEnabled
-XX:+AggressiveOpts
-XX:+HeapDumpOnOutOfMemoryError
-XX:OnOutOfMemoryError=kill -9 %p
-XX:ReservedCodeCacheSize=300M
```

### <h3>`config.properties`</h3>

```
coordinator=true
node-scheduler.include-coordinator=true
http-server.http.port=8080
query.max-memory=5GB
query.max-memory-per-node=1GB
query.max-total-memory-per-node =2GB
discovery-server.enabled=true
discovery.uri = http://hadoop04:8080
```
* `coordinator`
  * 允许此presto实例被当做coordinator(调度器)
* `node-scheduler.include-coordinator`
  * 允许在coordinator(调度器)节点上work,此操作会影响性能(本次测试的为单节点)
* `http-server.http.port`
  * HTTP客户端的端口号设置
* `query.max-memory`
  * 查询可消耗总的最大内存量(所有节点查询消耗内存总和)
* `query.max-memory-per-node`
  * 单节点查询消耗最大内存量
* `query.max-total-memory-per-node`
  * 单节点总的内存量
* `discovery-server.enabled`
  * 注册服务,相当于`注册中心`
* `discovery.uri`
  * discovery的服务器URI

### <h3>`log.properties`</h3>

```
com.facebook.presto = ERROR
```
* 日志等级
  * `INFO` `DEBUG` `WARN` `ERROR`

### <h3>`catalog`</h3>

> `hive.properties`

```
connector.name=hive-hadoop2
hive.metastore.uri=thrift://hadoop04:9083
hive.config.resources=/home/yetao_yang/hadoop/hadoop-2.9.2/etc/hadoop/core-site.xml,/home/yetao_yang/hadoop/hadoop-2.9.2/etc/hadoop/hdfs-site.xml
```

* `connector.name`
  * 连接的名称
* `hive.metastore.uri`
  * 需要与hive的metastore地址和端口对应
* `hive.config.resources`
  * 需要与hadoop集群的配置路径对应
* <font style="color : red">更多配置信息请千万presto官网</font>

### 启动presto服务端

* 命令
  * `./bin/launcher run`
    * 前台启动
  * `./bin/launcher start`
    * 后台启动

### 安装presto的客户端

> [客户端下载地址](https://repo1.maven.org/maven2/com/facebook/presto/presto-cli/0.218/presto-cli-0.218-executable.jar)
启动命令见[官网教程](https://prestodb.github.io/docs/current/installation/cli.html)

`presto/presto --server localhost:8080 --catalog hive --schema default`
