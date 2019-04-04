### HDFS的优缺点
* 优点
  * 高容错性
    * 数据自动保存多个副本。它通过增加副本的形式,提高容错性;
    * 某一个副本丢失以后，它可以自动恢复。
  * 适合大数据处理
    * 数据规模：能够处理数据规模达到GB、TB、甚至PB级别的数据
    * 文件规模：能够处理百万规模以上的文件数量，数量相当之大
  * 流式数据访问,保证数据的一致性
  * 可构建在廉价机器上，通过多副本机制，提高可靠性
* 缺点
  * 不适合低延时数据访问，比如毫秒级的存储数据，是做不到的
  * 无法高效的对大量小文件进行存储
    * 存储大量小文件的话，它会占用NameNode大量的内存来存储文件、目录和块信息。这样是不可取的，因为NameNode的内存总是有限的
    * 小文件存储的寻址时间会超过读取时间，它违反了HDFS的设计目标
  * 不支持并发写入、文件随机修改
    * 一个文件只能有一个写，不允许多个线程同时写
    * 仅支持数据append（追加），不支持文件的随机修改



### 常用基础命令

* **显示目录信息**
  * ` hadoop fs -ls /`

* **在hdfs上创建目录**
  * `hadoop fs -mkdir -p /yetao_yang/testMK`

* **从本地剪切粘贴到hdfs**
  * `hadoop fs -moveFromLocal ./bemove.txk /yetao_yang/testMK`
    ```shell
    [yetao_yang@hadoop01 ~]$ touch bemove.txk
    [yetao_yang@hadoop01 ~]$ ll
    总用量 0
    -rw-rw-r--. 1 yetao_yang yetao_yang  0 3月  26 16:17 bemove.txk
    drwxrwxr-x. 4 yetao_yang yetao_yang 65 3月  25 17:43 hadoop
    drwxrwxr-x. 3 yetao_yang yetao_yang 60 3月  22 01:46 jdk
    [yetao_yang@hadoop01 ~]$ hadoop fs -moveFromLocal ./bemove.txk /yetao_yang/testMK
    [yetao_yang@hadoop01 ~]$ ll
    总用量 0
    drwxrwxr-x. 4 yetao_yang yetao_yang 65 3月  25 17:43 hadoop
    drwxrwxr-x. 3 yetao_yang yetao_yang 60 3月  22 01:46 jdk
    ```

* **从本地拷贝到dhfs**
  * `hadoop fs -put testCP.txt /yetao_yang/testCP`

  * `hadoop fs -copyFromLocal testCP.txt /yetao_yang/testCP`

* **从hdfs拷贝到本地**
  * `hadoop fs -get /yetao_yang/output/part-r-00000 ./`

  * `hadoop fs -copyToLocal /yetao_yang/output/part-r-00000 ./`
    ```shell
    [yetao_yang@hadoop01 ~]$ hadoop fs -copyToLocal /yetao_yang/output/part-r-00000 ./
    [yetao_yang@hadoop01 ~]$ cat part-r-00000
    a	3
    b	2
    c	1
    d	4
    e	2
    f	8
    ```

* **从hdfs的一个路径拷贝到hdfs的另一个路径**
  * `hadoop fs -cp /yetao_yang/output/part-r-00000 /yetao_yang/testMK/kkk.txt`

* **在hdfs中移动文件**
  * `hadoop fs -mv /yetao_yang/testMK/kkk.txt /yetao_yang/input/`

* **合并下载多个文件**
  * 比如hdfs `/yetao_yang/test/`目录下有多个文件,要把这下面的所有文件下载下来
    * `hadoop fs -getmerge /yetao_yang/test/* ./merge.txt`

* **显示文件内容**
  * `hadoop fs -cat /yetao_yang/output/part-r-00000`
    ```shell
    [yetao_yang@hadoop01 ~]$ hadoop fs -cat /yetao_yang/output/part-r-00000
    a	3
    b	2
    c	1
    d	4
    e	2
    f	8
    ```

* **追加一个文件到已存在文件的末尾**
  * `hadoop fs -appendToFile testApp.txt /yetao_yang/testMK/bemove.txk`
    ```shell
    [yetao_yang@hadoop01 ~]$ touch testApp.txt
    [yetao_yang@hadoop01 ~]$ vi testApp.txt
    [yetao_yang@hadoop01 ~]$ cat testApp.txt
    aaa
    bbb
    ccc
    ddd
    [yetao_yang@hadoop01 ~]$ ^C
    [yetao_yang@hadoop01 ~]$ hadoop fs -appendToFile testApp.txt /yetao_yang/testMK/bemove.txk
    [yetao_yang@hadoop01 ~]$ hadoop fs -cat /yetao_yang/testMK/bemove.txk
    aaa
    bbb
    ccc
    ddd
    ```
* **删除文件或文件夹**
  * `hadoop fs -rm /yetao_yang/output/part-r-00000`


* **统计文件夹的大小信息**
  * `hadoop fs -du /yetao_yang/output`
    ```shell
    [yetao_yang@hadoop01 ~]$ hadoop fs -du /yetao_yang/output
    0   /yetao_yang/output/_SUCCESS
    24  /yetao_yang/output/part-r-00000
    ```

* **设置hdfs文件副本数量**
  * `hadoop fs -setrep 10 /yetao_yang/input/wc.input`
    * 设置10,但datanode只有三个所以实际副本只有三个


### HDFS的客戶端操作

* 在电脑上配置好`HADOOP_HOME`,具体[网上](http://www.baidu.com)查

* 新建java项目,并加入相关依赖

  ```xml
  <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-common -->
  <dependency>
      <groupId>org.apache.hadoop</groupId>
      <artifactId>hadoop-common</artifactId>
      <version>2.9.2</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-client -->
  <dependency>
      <groupId>org.apache.hadoop</groupId>
      <artifactId>hadoop-client</artifactId>
      <version>2.9.2</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/org.apache.hadoop/hadoop-hdfs -->
  <dependency>
      <groupId>org.apache.hadoop</groupId>
      <artifactId>hadoop-hdfs</artifactId>
      <version>2.9.2</version>
  </dependency>
  ```

* 代码演示

```java

@SpringBootTest
public class HdfsTestApplicationTests {
    Configuration configuration = new Configuration();
    //要在windows下配置ip地址映射
    URI uri = new URI("hdfs://hadoop01:9000");
    String user = "yetao_yang";


    // 创建文件夹
    @Test
    public void mkDir() throws IOException, InterruptedException {
        FileSystem fileSystem = FileSystem.get(uri,configuration,user);
        Path path = new Path("/hdfsDemo/mkdirs/mkdir02");
        fileSystem.mkdirs(path);
        fileSystem.close();
    }

    // 上传文件到指定目录
    @Test
    public void uploadFile() throws IOException, InterruptedException {
        Configuration configuration = new Configuration();
        // 配置文件的副本数 （1）客户端代码中设置的值 >（2）classpath下的用户自定义配置文件 >（3）然后是服务器的默认配置
        configuration.set("dfs.replication","5");
        FileSystem fileSystem = FileSystem.get(uri, configuration,user);
        // 源文件
        Path srcPath = new Path("F:\\srcFile.txt");
        // 目标文件
        Path dipPath = new Path("/upload/dip02.txt");
        fileSystem.copyFromLocalFile(srcPath,dipPath);
        fileSystem.close();
    }

    // 从hdfs下载到本地
    @Test
    public void downLoadFile() throws IOException, InterruptedException {
        FileSystem fileSystem = FileSystem.get(uri, configuration,user);
        // 源文件
        Path srcPath = new Path("/upload/dip02.txt");
        // 目标文件
        Path dipPath = new Path("F:\\downLoad.txt");
        fileSystem.copyToLocalFile(false, // 是否删除源文件
                srcPath, // 源文件
                dipPath, // 目标文件
                true // 是否采用本地的文本校验 (false会多产生一个校验的文件)
        );
        fileSystem.close();
    }

    // 文件夹的删除
    @Test
    public void deleteDir() throws IOException, InterruptedException {
        FileSystem fileSystem = FileSystem.get(uri, configuration,user);
        Path deleteDir = new Path("/hdfsDemo/mkdirs/mkdir01");
        fileSystem.delete(deleteDir, // 删除的路径
                false //是否递归删除
        );
        fileSystem.close();
    }

    // 更改文件名称
    @Test
    public void renameFile() throws IOException, InterruptedException {
        FileSystem fileSystem = FileSystem.get(uri, configuration,user);
        Path renameFile = new Path("/upload/dip.txt");
        Path afterFile = new Path("/upload/dip01.txt");
        fileSystem.rename(renameFile,afterFile);
        fileSystem.close();
    }

    // 文件详情的查看
    @Test
    public void readFileInfo() throws IOException, InterruptedException {
        FileSystem fileSystem = FileSystem.get(uri, configuration,user);
        Path file = new Path("/");
        RemoteIterator<LocatedFileStatus> listfiles = fileSystem.listFiles(
                file,
                true // 是否递归
        );
        while (listfiles.hasNext()) {
            System.out.println("------------------------------------------------------");
            LocatedFileStatus fileStatus = listfiles.next();
            System.out.println("文件名为 : " + fileStatus.getPath().getName());
            System.out.println("文件大小为 : " + fileStatus.getLen());
            System.out.println("文件权限为　："+ fileStatus.getPermission());
            BlockLocation[] locations = fileStatus.getBlockLocations();
            System.out.print("文件的存放节点为 : ");
            for (BlockLocation location : locations) {
                String[] hosts = location.getHosts();
                for (String host : hosts) {
                    System.out.print(" " + host);
                }
            }
            System.out.println();
            System.out.println("------------------------------------------------------");
        }
        fileSystem.close();
        /**
         * ------------------------------------------------------
         * 文件名为 : dip01.txt
         * 文件大小为 : 21
         * 文件权限为　：rw-r--r--
         * 文件的存放节点为 :  hadoop01 hadoop03 hadoop02
         * ------------------------------------------------------
         * ------------------------------------------------------
         * 文件名为 : dip02.txt
         * 文件大小为 : 21
         * 文件权限为　：rw-r--r--
         * 文件的存放节点为 :  hadoop03 hadoop02 hadoop01
         * ------------------------------------------------------
         * ------------------------------------------------------
         * 文件名为 : wc.input
         * 文件大小为 : 40
         * 文件权限为　：rw-r--r--
         * 文件的存放节点为 :  hadoop03 hadoop02 hadoop01
         * ------------------------------------------------------
         * ------------------------------------------------------
         * 文件名为 : _SUCCESS
         * 文件大小为 : 0
         * 文件权限为　：rw-r--r--
         * 文件的存放节点为 :
         * ------------------------------------------------------
         * ------------------------------------------------------
         * 文件名为 : part-r-00000
         * 文件大小为 : 24
         * 文件权限为　：rw-r--r--
         * 文件的存放节点为 :  hadoop02 hadoop03 hadoop01
         * ------------------------------------------------------
         * ------------------------------------------------------
         * 文件名为 : bemove.txk
         * 文件大小为 : 16
         * 文件权限为　：rw-r--r--
         * 文件的存放节点为 :  hadoop02 hadoop03 hadoop01
         * ------------------------------------------------------
         */
    }

    // 文件和文件夹的判断
    @Test
    public void isFileOrDir() throws IOException, InterruptedException {
        FileSystem fileSystem = FileSystem.get(uri, configuration,user);
        Path file = new Path("/");
        FileStatus[] fileStatuses = fileSystem.listStatus(file);
        for (FileStatus fileStatus : fileStatuses) {
            if (fileStatus.isFile())
                System.out.println("文件 : " + fileStatus.getPath().getName());
            else
                System.out.println("文件夹 : " + fileStatus.getPath().getName());
        }
        /**
         * 文件夹 : hdfsDemo
         * 文件夹 : tmp
         * 文件夹 : upload
         * 文件夹 : yetao_yang
         */
    }

    // 上传文件通过IO流
    @Test
    public void uploadFileByIO () throws IOException, InterruptedException {
        FileSystem fileSystem = FileSystem.get(uri, configuration,user);
        // 创建输入流
        FileInputStream fileInputStream = new FileInputStream(new File("F:\\阿里巴巴java开发规范.pdf"));
        Path outPath = new Path("/upload/阿里巴巴java开发规范.pdf");
        // 获取输出流
        FSDataOutputStream fsDataOutputStream = fileSystem.create(outPath);
        // 进行流的拷贝
        IOUtils.copyBytes(fileInputStream, fsDataOutputStream,configuration);// 该方法会自动的关闭 fileInputStream 和 fsDataOutputStream
        fileSystem.close();
    }

    // 通过IO流从hdfs下载文件
    @Test
    public void downLoadFileByIO () throws IOException, InterruptedException {
        FileSystem fileSystem = FileSystem.get(uri, configuration,user);
        Path inPath = new Path("/upload/阿里巴巴java开发规范.pdf");
        // 输入流
        FSDataInputStream fsDataInputStream = fileSystem.open(inPath);
        // 输出流
        FileOutputStream fileOutputStream = new FileOutputStream("F:\\阿里巴巴java开发规范02.pdf");
        IOUtils.copyBytes(fsDataInputStream,fileOutputStream,configuration);
        fileSystem.close();
    }

    // 下载第一块
    @Test
    public void readFileSeek1() throws IOException, InterruptedException{
        // 1 获取文件系统
        FileSystem fileSystem = FileSystem.get(uri, configuration,user);
        // 2 获取输入流
        FSDataInputStream fis = fileSystem.open(new Path("/hadoop-2.9.2.tar.gz"));
        // 3 创建输出流
        FileOutputStream fos = new FileOutputStream(new File("e:/hadoop-2.9.2.tar.gz.part1"));
        // 4 流的拷贝
        byte[] buf = new byte[1024];

        for(int i =0 ; i < 1024 * 128; i++){
            fis.read(buf);
            fos.write(buf);
        }
        // 5关闭资源
        IOUtils.closeStream(fis);
        IOUtils.closeStream(fos);
    }

    // 下载第二块
    @Test
    public void readFileSeek2() throws IOException, InterruptedException{
        // 1 获取文件系统
        FileSystem fileSystem = FileSystem.get(uri, configuration,user);
        // 2 打开输入流
        FSDataInputStream fis = fileSystem.open(new Path("/hadoop-2.9.2.tar.gz"));
        // 3 定位输入数据位置
        fis.seek(1024*1024*128);
        // 4 创建输出流
        FileOutputStream fos = new FileOutputStream(new File("e:/hadoop-2.9.2.tar.gz.part2"));
        // 5 流的对拷
        IOUtils.copyBytes(fis, fos, configuration);
        // 6 关闭资源
        IOUtils.closeStream(fis);
        IOUtils.closeStream(fos);
    }

    /**
     *
     * 在window命令窗口中执行
     * type hadoop-2.9.2.tar.gz.part2 >> hadoop-2.9.2.tar.gz.part1
     *
     */

    public HdfsTestApplicationTests() throws URISyntaxException {
    }
}
```

### HDFS写数据流程
  * 客户端向`namenode`请求上传某某文件.
  * namenode判断该用户是否有上传权限
  * (如果可以上传) 客户端将文件进行切分,并请求上传第一块的数据
  * namenode返回datanode给客户端(返回的datanode是最优的几个datanode)
  * 客户端跟返回的第一个datanode建立通道,并开始把数据传送到该datanode
  * 传送完成后开始上传第二块

    ![](./img/image01.jpg)

### 网络拓扑概念
* 节点距离
  * 两个节点到达最近的共同祖先的距离总和。

    ![](./img/image02.jpg)

### HDFS读数据流程

  ![](./img/image03.jpg)

### NN与2NN的工作机制
  ![](./img/image04.jpg)

### namenode故障处理

* `将SecondaryNameNode中数据拷贝到NameNode存储数据的目录`

* 重启`namenode`

### 集群的安全模式

* 概念
  * `集群处于安全模式，不能执行重要操作（写操作）。集群启动完成后，自动退出安全模`


* 基本语法
  * `bin/hdfs dfsadmin -safemode get`
    * **查看安全模式状态**
  * `bin/hdfs dfsadmin -safemode enter`
    * **进入安全模式状态**
  * `bin/hdfs dfsadmin -safemode leave`
    * **离开安全模式状态**
  * `bin/hdfs dfsadmin -safemode wait`
    * **等待安全模式状态，监控安全模式**

### DataNode的工作机制

* 一个数据块在DataNode上以文件形式存储在磁盘上，包括两个文件，一个是数据本身，一个是元数据包括数据块的长度，块数据的校验和，以及时间戳。
* DataNode启动后向NameNode注册，通过后，周期性（1小时）的向NameNode上报所有的块信息。
* 心跳是每3秒一次，心跳返回结果带有NameNode给该DataNode的命令如复制块数据到另一台机器，或删除某个数据块。如果超过10分钟没有收到某个DataNode的心跳，则认为该节点不可用。
* 集群运行中可以安全加入和退出一些机器。

![](./img/image05.jpg)

### 数据的完整性

* 当DataNode读取block的时候，它会计算checksum。
* 如果计算后的checksum，与block创建时值不一样，说明block已经损坏。
* client读取其他DataNode上的block。
* datanode在其文件创建后周期验证checksum

![](./img/image06.jpg)

### 服役新数据节点

> 随着公司业务的增长，数据量越来越大，原有的数据节点的容量已经不能满足存储数据的需求，需要在原有集群基础上动态添加新的数据节点。

* 准备一台虚拟机
* 修改ip地址和主机名
* 添加免密登录
* 安装java与hadoop
* 修改hadoop的配置
  * 修改`hdfs-site.xml`文件,把副本数改为4
  * 修改`slaves`文件,添加新的`datanode`
* 把hadoop发送到新的节点上
* 刷新`namenode`节点
  * `hdfs dfsadmin -refreshNodes`
* 更新resourcemanager节点
  * `yarn rmadmin -refreshNodes`
* 单独启动`数据节点`和`节点管理器`
  * `sbin/hadoop-daemon.sh start datanode`
  * `sbin/yarn-daemon.sh start nodemanager`
* 如果数据不均衡,通过命令实现集群的再均衡
  * `./start-balancer.sh`

### Hadoop2.x的新特性

* 集群间的数据拷贝
  ```shell
  [yetao_yang@hadoop01 ]$  hadoop discp
  hdfs://haoop01:9000/user/atguigu/hello.txt hdfs://hadoop02:9000/user/atguigu/hello.txt
  ```

### hadoop的存档

> **存小文件的弊端 : ** 每个文件均按块存储，每个块的元数据存储在NameNode的内存中，因此hadoop存储小文件会非常低效。因为大量的小文件会耗尽NameNode中的大部分内存。但注意，存储小文件所需要的磁盘容量和存储这些文件原始内容所需要的磁盘空间相比也不会增多。例如，一个1MB的文件以大小为128MB的块存储，使用的是1MB的磁盘空间，而不是128MB。

* 解决存储小文件办法之一
  > Hadoop存档文件或HAR文件，是一个更高效的文件存档工具，它将文件存入HDFS块，在减少NameNode内存使用的同时，允许对文件进行透明的访问。具体说来，Hadoop存档文件对内（对于文件本身）还是一个一个独立文件，对NameNode（对外）而言却是一个整体，减少了NameNode的内存。

### HDSF HA 高可用

> 所谓HA（high available），即高可用（7*24小时不中断服务）。

> 实现高可用最关键的策略是消除单点故障。HA严格来说应该分成各个组件的HA机制：HDFS的HA和YARN的HA。

> Hadoop2.0之前，在HDFS集群中NameNode存在单点故障（SPOF）。

  >
* NameNode主要在以下两个方面影响HDFS集群
* NameNode机器发生意外，如宕机，集群将无法使用，直到管理员重启
*	NameNode机器需要升级，包括软件、硬件升级，此时集群也将无法使用
* HDFS HA功能通过配置Active/Standby两个nameNodes实现在集群中对NameNode的热备来解决上述问题。如果出现故障，如机器崩溃或机器需要升级维护，这时可通过此种方式将NameNode很快的切换到另外一台机器。
* **HDFS-HA工作机制**
  * **通过双namenode消除单点故障**

* HDFS-HA工作要点
*
