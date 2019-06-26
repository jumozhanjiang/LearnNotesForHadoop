> <h4 style="color: red">Centos 里面一定要安装fuser这个插件 命令 : `yum install psmisc` PS: 切记一定要安装,要不然namenode不能自动切换到active</h4>

> 集群说明

系统 | hostname | ip | hadoop版本 | java版本
--|--|--|--|--
CentOS Linux release 7.6.1810 (Core)|bigdata01|172.17.57.229|Hadoop 2.8.5|1.8.0_211
CentOS Linux release 7.6.1810 (Core)|bigdata02|172.17.57.234|Hadoop 2.8.5|1.8.0_211
CentOS Linux release 7.6.1810 (Core)|bigdata03|172.17.57.226|Hadoop 2.8.5|1.8.0_211

> 集群规划

|hadoop01|hadooop02|hadoop03
---|---|---
HDFS|NameNode</br></br>DataNode|DataNode|SecondaryNameNode</br></br>DataNode
YARN|NodeManager|ResourceManager</br></br>NodeManager|NodeManager

### zookeeper的安装参考zookeeper的笔记

### 配置免密登录
  ```sh
  [yetao_yang@bigdata03 ~]$ ssh-keygen -t rsa
  Generating public/private rsa key pair.
  Enter file in which to save the key (/home/yetao_yang/.ssh/id_rsa):
  Created directory '/home/yetao_yang/.ssh'.
  Enter passphrase (empty for no passphrase):
  Enter same passphrase again:
  Your identification has been saved in /home/yetao_yang/.ssh/id_rsa.
  Your public key has been saved in /home/yetao_yang/.ssh/id_rsa.pub.
  The key fingerprint is:
  SHA256:yG2zOMq56cs5yK0S5NtfTDUNZxmYbOZ0T8IhyseYM1w yetao_yang@bigdata03
  The key's randomart image is:
  +---[RSA 2048]----+
  |        .E+=+    |
  |      o *B*= .   |
  |       O==..+    |
  | .   . o=..  .   |
  |o     o.S        |
  |..    oo o       |
  | ooo  oo.        |
  |..o+o=..         |
  | ..o%=           |
  +----[SHA256]-----+
  [yetao_yang@bigdata03 ~]$ ssh-copy-id bigdata03
  /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/yetao_yang/.ssh/id_rsa.pub"
  The authenticity of host 'bigdata03 (172.17.57.226)' can't be established.
  ECDSA key fingerprint is SHA256:6AGtLWnSlG5AfTew3b59FeREHNrZT5607KGfYhDa/VY.
  ECDSA key fingerprint is MD5:1f:12:79:ac:d7:50:fc:0e:f7:e1:97:fe:2d:e8:7d:1b.
  Are you sure you want to continue connecting (yes/no)? yes
  /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
  /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
  yetao_yang@bigdata03's password:

  Number of key(s) added: 1

  Now try logging into the machine, with:   "ssh 'bigdata03'"
  and check to make sure that only the key(s) you wanted were added.

  [yetao_yang@bigdata03 ~]$ ssh-copy-id bigdata02
  /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/yetao_yang/.ssh/id_rsa.pub"
  The authenticity of host 'bigdata02 (172.17.57.234)' can't be established.
  ECDSA key fingerprint is SHA256:SNusOQIVlsyhQkpsNWZVRW58H6bfDwVm1M76yoTpifU.
  ECDSA key fingerprint is MD5:97:4d:51:54:66:26:54:57:74:7c:3b:04:86:69:8d:bf.
  Are you sure you want to continue connecting (yes/no)? yes
  /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
  /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
  yetao_yang@bigdata02's password:

  Number of key(s) added: 1

  Now try logging into the machine, with:   "ssh 'bigdata02'"
  and check to make sure that only the key(s) you wanted were added.

  [yetao_yang@bigdata03 ~]$ ssh-copy-id bigdata01
  /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/yetao_yang/.ssh/id_rsa.pub"
  The authenticity of host 'bigdata01 (172.17.57.229)' can't be established.
  ECDSA key fingerprint is SHA256:hBPvCOcvKn9x7hmeS31SoxDJQH4jVxWx3h3C3rgFj+o.
  ECDSA key fingerprint is MD5:73:2f:cd:5d:6b:34:22:0f:37:d4:24:8e:44:2f:cb:a4.
  Are you sure you want to continue connecting (yes/no)? yes
  /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
  /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
  yetao_yang@bigdata01's password:

  Number of key(s) added: 1

  Now try logging into the machine, with:   "ssh 'bigdata01'"
  and check to make sure that only the key(s) you wanted were added.
  ```
  * `bigdata02` `bigdata03`也执行相同操作

### 从[官网](https://hadoop.apache.org/)下载对应版本的hadoop

### 在当前用户下配置环境变量

* 配置环境变量
  * 在当前用户下的`.bashrc`文件中配置
    * `/home/yetao_yang/hadoop/hadoop-2.8.5`目录是下载hadoop对应的解压文件夹路径

  ```shell
  export HADOOP_HOME=/home/yetao_yang/hadoop/hadoop-2.8.5
  export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
  export PATH=${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:$PATH
  ```
* 刷新环境变量文件配置
  * `source ~/.bashrc`
* 查看hadoop版本
  * `hadoop version`

### 配置HDFS高可用

* 修改`hadoop-env.sh`文件里面的JAVA_HOME路径为绝对路径
  * 使用`echo $JAVA_HOME`查看路径

* 配置`hdfs-site.xml`
  ```xml
  <!-- 指定副本数 -->
  <property>
        <name>dfs.replication</name>
        <value>3</value>
  </property>

  <!-- namenode对外暴露的集群名称 -->
  <property>
    <name>dfs.nameservices</name>
    <value>mycluster</value>
  </property>

  <!-- 两个namenode叫什么名称 -->
  <property>
    <name>dfs.ha.namenodes.mycluster</name>
    <value>nn1,nn2</value>
  </property>

  <!-- namenode的通信地址 -->
  <property>
    <name>dfs.namenode.rpc-address.mycluster.nn1</name>
    <value>bigdata01:8020</value>
  </property>
  <!-- namenode的通信地址 -->
  <property>
    <name>dfs.namenode.rpc-address.mycluster.nn2</name>
    <value>bigdata03:8020</value>
  </property>

  <!-- namenode的外部端口 -->
  <property>
    <name>dfs.namenode.http-address.mycluster.nn1</name>
    <value>bigdata01:50070</value>
  </property>
  <!-- namenode的外部端口 -->
  <property>
    <name>dfs.namenode.http-address.mycluster.nn2</name>
    <value>bigdata03:50070</value>
  </property>

  <!-- qjournal 共享数据的配置地址 至少要三台 -->
  <property>
    <name>dfs.namenode.shared.edits.dir</name>
    <value>qjournal://bigdata01:8485;bigdata02:8485;bigdata03:8485/mycluster</value>
  </property>

  <!-- 代理类 -->
  <property>
    <name>dfs.client.failover.proxy.provider.mycluster</name>
    <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
  </property>

  <!-- 隔离机制 -->
  <property>
    <name>dfs.ha.fencing.methods</name>
    <value>sshfence</value>
  </property>
  <!-- 采用哪个用户的ssh -->
  <property>
    <name>dfs.ha.fencing.ssh.private-key-files</name>
    <value>/home/yetao_yang/.ssh/id_rsa</value>
  </property>
  <!-- 开启故障自动转移 -->
  <property>
    <name>dfs.ha.automatic-failover.enabled</name>
    <value>true</value>
  </property>
  ```

* 配置`core-site.xml`
  ```xml
  <!-- 指定HDFS中NameNode的地址 -->
  <property>
      <name>fs.defaultFS</name>
      <value>hdfs://mycluster</value>
  </property>

  <!-- 指定hadoop运行时产生文件的存储目录 -->
  <property>
      <name>hadoop.tmp.dir</name>
      <value>/home/yetao_yang/hadoop/data/hadoop/tmp</value>
  </property>

  <!-- journalnode共享数据的存放路径 -->
  <property>
    <name>dfs.journalnode.edits.dir</name>
    <value>/home/yetao_yang/hadoop/data/jn</value>
  </property>
  <!-- zk的服务器地址 -->
  <property>
    <name>ha.zookeeper.quorum</name>
    <value>bigdata01:2181,bigdata02:2181,bigdata03:2181</value>
  </property>
  ```

* 配置`slaves`
  ```
  bigdata01
  bigdata02
  bigdata03
  ```

### 配置YARN的高可用

* 修改`yarn-env.sh`文件里面的JAVA_HOME路径为绝对路径
  * 使用`echo $JAVA_HOME`查看路径
* 修改`yarn-site.xml`文件
  ```xml
  <property>
   <name>yarn.nodemanager.aux-services</name>
   <value>mapreduce_shuffle</value>
  </property>

  <!--启用resourcemanager ha-->
  <property>
    <name>yarn.resourcemanager.ha.enabled</name>
    <value>true</value>
  </property>
  <!--声明两台resourcemanager的地址-->
  <property>
    <name>yarn.resourcemanager.cluster-id</name>
    <value>cluster-yarn</value>
  </property>
  <property>
    <name>yarn.resourcemanager.ha.rm-ids</name>
    <value>rm1,rm2</value>
  </property>
  <property>
    <name>yarn.resourcemanager.hostname.rm1</name>
    <value>bigdata01</value>
  </property>
  <property>
    <name>yarn.resourcemanager.hostname.rm2</name>
    <value>bigdata02</value>
  </property>
  <!-- 指定rm1客户端的地址 -->
  <property>
    <name>yarn.resourcemanager.webapp.address.rm1</name>
    <value>bigdata01:8088</value>
  </property>
  <!-- 指定rm2客户端的地址 -->
  <property>
    <name>yarn.resourcemanager.webapp.address.rm2</name>
    <value>bigdata02:8088</value>
  </property>
  <!--启用自动恢复-->
  <property>
    <name>yarn.resourcemanager.recovery.enabled</name>
    <value>true</value>
  </property>
  <!--指定zookeeper集群的地址-->
  <property>
    <name>yarn.resourcemanager.zk-address</name>
    <value>bigdata01:2181,bigdata02:2181,bigdata03:2181</value>
  </property>
  <!--指定resourcemanager的状态信息存储在zookeeper集群-->
  <property>
    <name>yarn.resourcemanager.store.class</name>
    <value>org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore</value>
  </property>
  ```

* 修改`mapred-env.sh`文件里面的JAVA_HOME路径为绝对路径
  * 使用`echo $JAVA_HOME`查看路径
* 拷贝`mapred-site.xml.template`文件为`mapred-site.xml`并添加配置
  ```xml
  <!-- 指定mr运行在yarn上 -->
  <property>
      <name>mapreduce.framework.name</name>
      <value>yarn</value>
  </property>
  ```

### 配置日志聚集功能

* 在`yarn-site.xml`文件里面添加如下配置
  ```xml
  <!--日志查看地址-->
  <property>
    <name>yarn.log.server.url</name>
    <value>http://bigdata01:19888/jobhistory/logs/</value>
  </property>
  <!-- 日志聚集功能 -->
  <property>
    <name>yarn.log-aggregation-enable</name>
    <value>true</value>
  </property>
  <!-- 日志保留时间设置为7天  -->
  <property>
    <name>yarn.log-aggregation.retain-seconds</name>
    <value>604800</value>
  </property>
  ```

* 在`mapred-site.xml`文件里面添加如下配置
  ```xml
  <!-- 配置历史服务器 -->
  <property>
    <name>mapreduce.jobhistory.address</name>
    <value>bigdata01:10020</value>
  </property>
  <property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>bigdata01:19888</value>
  </property>
  ```
  * **当拷贝配置后请把主机名改成对应的主机名**

### 启动
#### 启动HDFS
* 在各个`JournalNode`节点上启动`journalnode`服务
  * `sbin/hadoop-daemon.sh start journalnode`
* 在[nn1]上,对其进行格式化,并启动
  * `bin/hdfs namenode -format`
  * `sbin/hadoop-daemon.sh start namenode`
* 在[nn2]上,同步nn1的元数据信息
  * `bin/hdfs namenode -bootstrapStandby`
* 关闭所有的HDFS服务
  * `sbin/stop-dfs.sh`
* 启动Zookeeper集群
  * `bin/zkServer.sh start`
* 初始化HA在Zookeeper中的状态
  * `bin/hdfs zkfc -formatZK`


> <font style="color:red">以上步骤只需操作一次</font>

* 启动HDFS服务
  * `sbin/start-dfs.sh`
* 如果没有启动ZKFC就要手动启动了
  * `sbin/hadoop-daemon.sh start zkfc`

#### 启动YARN
* 在[rm1]上启动yarn
  * `sbin/start-yarn.sh`
* 在[rm2]上启动YARN
  * `sbin/yarn-daemon.sh start resourcemanager`
* 查看服务状态
  * `bin/yarn rmadmin -getServiceState rm1`
* 启动历史服务器
  * `mr-jobhistory-daemon.sh start historyserver`

### 查看每台机器的进程详情

* bigdata01
  ```sh
  [yetao_yang@bigdata01 hadoop]$ jps
  19697 JournalNode
  19889 DFSZKFailoverController
  19490 DataNode
  21683 JobHistoryServer
  19995 ResourceManager
  20539 NameNode
  6635 Jps
  16126 QuorumPeerMain
  20111 NodeManager
  ```

* bigdata02
  ```sh
  [yetao_yang@bigdata02 hadoop]$ jps
  17841 JournalNode
  17937 NodeManager
  18273 ResourceManager
  25236 JobHistoryServer
  17738 DataNode
  15964 QuorumPeerMain
  26013 Jps
  ```

* bigdata03
  ```sh
  [yetao_yang@bigdata03 hadoop]$ jps
  29266 Jps
  18213 JournalNode
  18341 DFSZKFailoverController
  18422 NodeManager
  15950 QuorumPeerMain
  18110 DataNode
  18031 NameNode
  ```

### 验证HA

> **HDFS:** <br/> 分别进入两台namenode的50070页面查看状态<br/>然后在命令行界面把状态为`active`的namenode进程杀掉<br/>再刷新状态为`standby`的namenode是否变成了`active`<br/> **YARN:** <br/> 查看两台rm的8088界面,是否最终都跳转到了一个地址
