### 什么是maxwell
> maxwell是一个mysql的binlog解析器, 用于把binlog的日志解析为json格式并传入kafka等消息中间件中

### 配置maxwell
> [下载地址](https://github.com/zendesk/maxwell/releases/download/v1.22.3/maxwell-1.22.3.tar.gz)附件里面有已下载好的

* 给mysql创建一个maxwell用户
  * `GRANT ALL on *.* to 'maxwell'@'%' identified by '123456';`
  * `GRANT SELECT, REPLICATION CLIENT,REPLICATION SLAVE on *.* to 'maxwell'@'%';`
  * `flush privileges;`
* 开启mysql的`binlog`功能
  * `vim /etc/my.cnf`
    ```properties
    # mysql的id 集群用
    server-id=1
    # binlog存放路径
    log-bin=/var/lib/mysql/mysql-bin
    # binlog的存储格式
    binlog_format=row
    ```
  * 重启mysql
    * `systemctl restart mysqld`
* 拷贝`maxwell`中的`config.properties.example`为`config.properties`
* 修改`config.properties`文件**定义默认值配置,也可以在启动命令里面手动指定**
  ```properties
  # 监控级别
  log_level=info
  # kafka配置
  producer=kafka
  # kafka客户端配置
  kafka.bootstrap.servers=bigdata01:9092,bigdata02:9092,bigdata03:9092
  # topic配置
  kafka_topic=maxwell

  # mysql login info
  # 需要监控的mysql主机
  host=bigdata03
  # 用户名
  user=maxwell
  # 密码
  password=123456
  # 只监控winnower DB下的所有表
  filter=exclude: *.*,include: winnower.*
  # 用js的方式设置更高级的过滤器
  javascript=/home/yetao_yang/maxwell/jsFilter/filter.js
  ```
* javascript模式过滤器设置**更多的设置请参考[官方配置](https://github.com/zendesk/maxwell/blob/master/src/example/filter.js)**
  ```javascript
  function process_row(row) {
      // 自定义过滤器
      /*
      * 因为不是所有表字段的内容修改都需要同步到数据仓库里面去的,因为有些字段是不参与计算的, 只是为了页面显示 就没有统计的价值
      * 如 新闻表里面 有 new_info: 新闻具体内容, new_img: 新闻缩略图片base64格式的,这两个字段就不用参与统计
      * 判断如果只是这两个字段发生了更改就不发送到kafka,如果还有其他字段,就把这两个字段给删除掉
      * */
      try {
          // 判断DB是否为winnower
          if ( row.database == "winnower" ) {
              // 判断变更的表是否为news
              if ( row.table == "news" ) {
                  // 操作类型是否为update类型
                  if (row.type == "update") {
                      // 判断更改内容是否包含new_info字段,如果包含就删除该字段
                      if (row.old_data.new_info !== "undefined" && row.old_data.new_info !== null) {
                          var old_data1 =  row.old_data;
                          old_data1.remove("new_info");
                          row.old_data = old_data1;
                      }
                      // 判断更改内容是否包含new_img字段,如果包含就删除该字段
                      if (row.old_data.new_img !== "undefined" && row.old_data.new_img !== null) {
                          var old_data2 =  row.old_data;
                          old_data2.remove("new_img");
                          row.old_data = old_data2;
                      }
                      // 判断除了new_ifo,new_img字段外是否还有其他字段进行了修改
                      if (row.old_data.size() == 0) {
                          // 如果没有其它字段进行修改 则不提交到kafka
                          row.suppress();
                      }
                  }
                  // 删除data里面的new_img和new_info字段
                  var data1 =  row.data;
                  data1.remove("new_img");
                  data1.remove("new_info");
                  row.data = data1;
              }
          }
      } catch (e) {
          logger.error(e);
      }
  }

  ```

* 启动`maxwell`
  * `./bin/maxwell`
* 查看kafka的`maxwell`topic
  * `~/kafka/kafka_2.12-2.2.0/bin/kafka-console-consumer.sh  --bootstrap-server bigdata01:9092 --from-beginning --topic maxwell`
  * 当mysql里面有数据发生变动时 kafka就会接收到消息 消息格式为
    ```json
    {"database":"winnower","table":"collect","type":"insert","ts":1563868600,"xid":6484,"commit":true,"data":{"id":8,"user_id":1,"info_id":87,"creat_time":1563868596773,"updata_time":1563868596773}}
    ```
