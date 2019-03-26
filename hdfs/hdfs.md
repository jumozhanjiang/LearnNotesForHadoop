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
