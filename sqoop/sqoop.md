### 什么是sqoop
> Apache Sqoop(TM)是一种旨在有效地在Apache Hadoop和诸如关系数据库等结构化数据存储之间传输大量数据的工具。<br/> Sqoop于2012年3月孵化出来，现在是一个顶级的Apache项目。<br/>最新的稳定版本是1.4.6。Sqoop2的最新版本是1.99.7。请注意，1.99.7与1.4.6不兼容，且没有特征不完整，它并不打算用于生产部署。

### sqoop的原理
> 将导入或导出命令翻译成mapreduce程序来实现。<br/>在翻译出的mapreduce中主要是对inputformat和outputformat进行定制。

### sqoop的安装
> [下载sqoop1.4.7](https://mirrors.tuna.tsinghua.edu.cn/apache/sqoop/1.4.7/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz)**注意请注意，1.99.7并不适合用于生产环境**

* 重命名`conf/sqoop-env-template.sh` 文件为 `conf/sqoop-env.sh`并对重命名的文件进行修改
  ```sh
  export HADOOP_COMMON_HOME=/home/yetao_yang/hadoop/hadoop-2.8.5
  export HADOOP_MAPRED_HOME=/home/yetao_yang/hadoop/hadoop-2.8.5
  export HIVE_HOME=/home/yetao_yang/hive/hive-2.3.5
  export ZOOKEEPER_HOME=/home/yetao_yang/zookeeper/zookeeper-3.4.14
  export ZOOCFGDIR=/home/yetao_yang/zookeeper/zookeeper-3.4.14
  export HBASE_HOME=/home/yetao_yang/hbase/hbase-2.0.5
  ```

* 把`mysql-connector-java-5.1.27-bin.jar`文件添加到`./sqoop/lib/`目录下

### 关系型数据到HDFS

* 全量导入

  ```sh
  ./bin/sqoop import \
  --connect jdbc:mysql://bigdata03:3306/winnower \
  --username root \
  --password 123456 \
  --table user \
  --target-dir /user/hive/user \
  --delete-target-dir \
  --num-mappers 1 \
  --fields-terminated-by "\b"
  ```

* 查询导入

  ```sh
  ./bin/sqoop import \
  --connect jdbc:mysql://bigdata03:3306/winnower \
  --username root \
  --password 123456 \
  --table user \
  --target-dir /user/hive/user \
  --delete-target-dir \
  --num-mappers 1 \
  --fields-terminated-by "\b"
  -- query ''
  ```
