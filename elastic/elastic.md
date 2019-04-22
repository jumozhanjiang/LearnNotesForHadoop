### ElasticSearch集群搭建

<h5 style="color:red">主机名</h5> | hadoop01 | hadoop02 | hadoop03 | hadoop04
:-- | :--: | :--: | :--: | :--:
<h5 style="color:red">ES版本</h5> | elasticsearch-7.0.0 | elasticsearch-7.0.0 | elasticsearch-7.0.0 | elasticsearch-7.0.0
<h5 style="color:red">主从关系</h5> |主节点1 | 从节点 | 从节点 | 主节点2

> 下载[ElasticSearch](https://www.elastic.co/cn/downloads/past-releases)

> 解压

> 修改`config/elasticsearch.yml`文件,添加以下内容



```
# 集群名称
cluster.name: log-pioneer
# 节点名称
node.name: node-1
# 节点地址
network.host: hadoop01
# 端口号
http.port: 9200
# master节点的地址
discovery.seed_hosts: ["hadoop01", "hadoop04"]
# 集群初始化master选举节点
cluster.initial_master_nodes: ["node-1", "node-4"]
node.attr.rack: r1
# 数据存放路径
path.data: /home/yetao_yang/elastic/elticsearch/data
# 日志存放路径
path.logs: /home/yetao_yang/elastic/elticsearch/logs
# 在达到多少个节点之后才会开始数据恢复
gateway.recover_after_nodes: 4
http.cors.enabled: true
http.cors.allow-origin: "*"
```

> 分别启动四台节点<br>
  `nohup ./bin/elasticsearch &`

> 浏览器下载`Elasticsearch Head`插件,查看集群状态<br>
  ![](./img/image01.jpg)

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

### [安装](https://www.elastic.co/cn/downloads/kibana)`kibana`


`./bin/kafka-topics.sh --list --zookeeper hadoop02:2181`
