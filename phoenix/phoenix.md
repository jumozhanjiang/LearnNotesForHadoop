# 集群前提
> 已成功搭建 **Hadoop高可用集群** , **Hive** , **Hbase高可用集群** , Hive已与Hbase进行了关联 , **Yarn历史服务器**

名称 | 版本号
- | -
Java | 1.8.0_211
Hadoop | 2.8.5
Hbase  |  2.0.5
Hive  |  2.3.6
Zookeeper  |  3.5.5
Kafka  |  2.2.0
Phoenix  |  5.0.0

# 安装Phoenix

* 在官网[下载Phoenix](http://phoenix.apache.org/download.html)
  - `本人下载的是5.0.0 因为安装的Hbase为2.0.x版本的`
* 把tar文件放在指定的文件夹
  ```sh
  [yyt@bigdata01 phoenix]$ pwd
  /home/yyt/opt/phoenix
  [yyt@bigdata01 phoenix]$ ll
  总用量 4
  drwxr-xr-x. 5 yyt yyt 4096 6月  26 2018 phoenix-5.0.0-HBase-2.0
  ```
* 配置环境变量
  - root用户修改`/etc/profile`文件
  - 非root用户修改`~/.bashrc`文件
  ```sh
  export PHOENIX_HOME=/home/yyt/opt/phoenix/phoenix-5.0.0-HBase-2.0
  export PATH=$PATH:$PHOENIX_HOME/bin
  ```
  - 刷新环境变量
    - `source /etc/profile` OR `source ~/.bashrc`
* 把`${PHOENIX_HOME}/lib/phoenix-5.0.0-HBase-2.0-server.jar`这个包软连接或拷贝到`${HBASE_HOME}\lib`下,然后重启`Hbase`
* 其它的机器重复该步骤
* 启动Phoenix
  - `sqlline.py HOST01,HOST02,HOST03:2181`
    ```sh
    [yyt@bigdata01 phoenix-5.0.0-HBase-2.0]$ sqlline.py bigdata01,bigdata02,bigdata03:2181
    Setting property: [incremental, false]
    Setting property: [isolation, TRANSACTION_READ_COMMITTED]
    issuing: !connect jdbc:phoenix:bigdata01,bigdata02,bigdata03:2181 none none org.apache.phoenix.jdbc.PhoenixDriver
    Connecting to jdbc:phoenix:bigdata01,bigdata02,bigdata03:2181
    SLF4J: Class path contains multiple SLF4J bindings.
    SLF4J: Found binding in [jar:file:/home/yyt/opt/phoenix/phoenix-5.0.0-HBase-2.0/phoenix-5.0.0-HBase-2.0-client.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: Found binding in [jar:file:/home/yyt/opt/hadoop/hadoop-2.8.5/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
    SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
    19/11/22 03:19:41 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
    Connected to: Phoenix (version 5.0)
    Driver: PhoenixEmbeddedDriver (version 5.0)
    Autocommit status: true
    Transaction isolation: TRANSACTION_READ_COMMITTED
    Building list of tables and columns for tab-completion (set fastconnect to true to skip)...
    137/137 (100%) Done
    Done
    sqlline version 1.2.0
    0: jdbc:phoenix:bigdata01,bigdata02,bigdata03>
    ```

# Phoenix的基础使用
```sh
# 查勘表
0: jdbc:phoenix:bigdata01,bigdata02,bigdata03> ! table
+------------+--------------+-------------+---------------+----------+------------+----------------------------+----------+
| TABLE_CAT  | TABLE_SCHEM  | TABLE_NAME  |  TABLE_TYPE   | REMARKS  | TYPE_NAME  | SELF_REFERENCING_COL_NAME  | REF_GENE |
+------------+--------------+-------------+---------------+----------+------------+----------------------------+----------+
|            | SYSTEM       | CATALOG     | SYSTEM TABLE  |          |            |                            |          |
|            | SYSTEM       | FUNCTION    | SYSTEM TABLE  |          |            |                            |          |
|            | SYSTEM       | LOG         | SYSTEM TABLE  |          |            |                            |          |
|            | SYSTEM       | SEQUENCE    | SYSTEM TABLE  |          |            |                            |          |
|            | SYSTEM       | STATS       | SYSTEM TABLE  |          |            |                            |          |
|            |              | student     | TABLE         |          |            |                            |          |
+------------+--------------+-------------+---------------+----------+------------+----------------------------+----------+
# 退出
0: jdbc:phoenix:bigdata01,bigdata02,bigdata03> ! quit
Closing: org.apache.phoenix.jdbc.PhoenixConnection
# 创建表
0: jdbc:phoenix:bigdata01,bigdata02,bigdata03> CREATE TABLE "peoples"
. . . . . . . . . . . . . . . . . . . . . . .> (
. . . . . . . . . . . . . . . . . . . . . . .>   id varchar primary key,
. . . . . . . . . . . . . . . . . . . . . . .>   "info"."name" varchar,
. . . . . . . . . . . . . . . . . . . . . . .>   "info"."age" TINYINT,
. . . . . . . . . . . . . . . . . . . . . . .>   "info"."sex" BOOLEAN,
. . . . . . . . . . . . . . . . . . . . . . .>   "info"."like" varchar
. . . . . . . . . . . . . . . . . . . . . . .> )
. . . . . . . . . . . . . . . . . . . . . . .> COLUMN_ENCODED_BYTES= 0;
No rows affected (1.393 seconds)
#CREATE TABLE "peoples"
#(
#  id varchar primary key,
#  "info"."name" varchar,
#  "info"."age" TINYINT,
#  "info"."sex" BOOLEAN,
#  "info"."like" varchar
#)
#COLUMN_ENCODED_BYTES= 0;
# 添加数据
0: jdbc:phoenix:bigdata01,bigdata02,bigdata03> upsert into "peoples"(id,"name","age","sex","like") values('1','yyt',18,true,'sleep');
1 row affected (0.054 seconds)
0: jdbc:phoenix:bigdata01,bigdata02,bigdata03> upsert into "peoples"(id,"name","age","sex","like") values('2','cxsm',23,false,'play');
1 row affected (0.013 seconds)
# 查找表
0: jdbc:phoenix:bigdata01,bigdata02,bigdata03> select * from "peoples " ;
+-----+-------+------+--------+--------+
| ID  | name  | age  |  sex   |  like  |
+-----+-------+------+--------+--------+
| 1   | yyt   | 18   | true   | sleep  |
| 2   | cxsm  | 23   | false  | play   |
+-----+-------+------+--------+--------+
2 rows selected (0.056 seconds)
# 删除表
0: jdbc:phoenix:bigdata01,bigdata02,bigdata03> DROP TABLE "student";
No rows affected (1.636 seconds)
```
