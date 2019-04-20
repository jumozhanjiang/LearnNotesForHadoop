### ElasticSearch集群搭建

<h5 style="color:red">主机名</h5> | hadoop01 | hadoop02 | hadoop03 | hadoop04
:-- | :--: | :--: | :--: | :--:
<h5 style="color:red">ES版本</h5> | elasticsearch-7.0.0 | elasticsearch-7.0.0 | elasticsearch-7.0.0 | elasticsearch-7.0.0
<h5 style="color:red">主从关系</h5> |主节点 | 从节点 | 从节点 | 从节点

> 下载[ElasticSearch](https://www.elastic.co/cn/downloads/past-releases)

> 解压



```
# 集群名称
cluster.name: es_dev
# 节点名称
node.name: node-1
#如果是master节点设置成true 如果是
node.master: true
#如果是data节点设置成true
node.data: false
# 数据存放路径
path.data: /home/yetao_yang/elastic/elticsearch/data
# 日志存放路径
path.logs: /home/yetao_yang/elastic/elticsearch/logs
# 本地主机名
network.host: hadoop01
# http端口
http.port: 9200
# tcp端口
transport.tcp.port: 9300
# 集群所有的节点
discovery.zen.ping.unicast.hosts: ["hadoop01", "hadoop02","hadoop03","hadoop04"]
# 主节点个数
discovery.zen.minimum_master_nodes: 2
# 内存锁住,以免把服务器搞瘫痪
bootstrap.memory_lock: true
bootstrap.system_call_filter: false
http.cors.enabled: true
http.cors.allow-origin: "*"
```

> 错误解析

* `max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]`
  * 切换到`root`用户并修改`/etc/security/limits.conf`文件,添加该配置
    ```
    *               soft    nofile          65536
    *               hard    nofile          65536
    ```
  * 刷新系统配置
    * `sysctl -p`
* `max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]`
  * 切换到`root`用户并修改`/etc/sysctl.conf`文件,添加该配置
    ```
    vm.max_map_count=262144
    ```
  * `sysctl -p`
