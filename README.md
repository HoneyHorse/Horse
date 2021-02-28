# Horse
A data synchronization tool between heterogeneous data sources built by spark

## 优势
- 可以同时输入多种不同类型的异构数据源
- 可以同时输出到多种不同类型的异构数据源

## Quick Start
整体项目每一个数据源的读写，只需要实现Reader和Writer即可，所有信息以Json形式的配置项存储在某个地方以供调用，
目前该项目初步以MySQL做为配置项进行加载运行，存储了数据源信息，Json信息，运行日志

```mysql
# MySQL各个建表语句如下
# t_resource_info	数据源表，存放了所有的数据源信息
CREATE TABLE `t_resource_info` (
  `id` bigint(20) NOT NULL COMMENT '唯一主键,用来标识数据源唯一id',
  `resource_name` varchar(100) NOT NULL COMMENT '代表此数据源的名字，union key',
  `resource_type` varchar(20) NOT NULL COMMENT 'e.g mysql、oracle、mongdb、hbase等等',
  `url` varchar(500) DEFAULT NULL COMMENT '数据源的连接地址',
  `port` varchar(500) DEFAULT NULL COMMENT '数据源的连接端口',
  `database` varchar(500) DEFAULT NULL COMMENT '数据源所对应的db库名',
  `username` varchar(500) DEFAULT NULL COMMENT '数据源的用户名',
  `password` varchar(500) DEFAULT NULL COMMENT '数据源的密码',
  `extend01` varchar(500) DEFAULT NULL COMMENT '预留扩展字段1',
  `extend02` varchar(500) DEFAULT NULL COMMENT '预留扩展字段2',
  `extend03` varchar(500) DEFAULT NULL COMMENT '预留扩展字段3',
  `description` text COMMENT '描述',
  PRIMARY KEY (`id`),
  UNIQUE KEY `resource_name_unique_index` (`resource_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

# t_sync_json_config 存放json数据信息的表
CREATE TABLE `t_sync_json_config` (
  `job_id` bigint(20) NOT NULL COMMENT '唯一主键,用来标识任务的唯一id',
  `reader_json` TEXT NOT NULL COMMENT '读数据的json存储',
  `writer_json` TEXT  NOT NULL COMMENT '读数据的json存储',
  `description` TEXT  DEFAULT NULL COMMENT '用一句话来描述你的业务',
  PRIMARY KEY (`job_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

# t_sync_exec_log 存放日志的表
CREATE TABLE `t_sync_exec_log` (
  `uuid` bigint(20) NOT NULL AUTO_INCREMENT,
  `job_id` bigint(20) DEFAULT NULL COMMENT '执行任务的t_sync_json_config表uuid',
  `sync_source_type` varchar(255) DEFAULT NULL COMMENT '同步任务的源类型 e.g mysql',
  `sync_dest_type` varchar(255) DEFAULT NULL COMMENT '同步目的类型 e.g hive',
  `init_reader_json` varchar(255) DEFAULT NULL COMMENT '读数据的json存储，如果有变量会被替换',
  `init_writer_json` varchar(255) DEFAULT NULL COMMENT '写数据的json存储，如果有变量会被替换',
  `sync_start_time` datetime DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '数据同步开始时间',
  `sync_end_time` datetime DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP COMMENT '数据同步结束时间',
  `sync_result` varchar(255) DEFAULT NULL COMMENT '数据同步成功or失败 e.g success or faild',
  `sync_row_count` bigint(20) DEFAULT NULL COMMENT '数据同步总记录数 e.g 10000',
  `sync_row_success_count` bigint(20) DEFAULT NULL COMMENT '数据同步总成功记录数 e.g 8983',
  `sync_log` varchar(5000) DEFAULT NULL COMMENT '数据同步日志信息',
  PRIMARY KEY (`uuid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

###### e.g Reader.Json配置如下，整体结构为：{"reader":[{},{},{},...]}
```Json
{
  "reader": [
    {
      "type": "hdfs",
      "class": "com.horse.rw.plugins.hdfs.HDFSReader",
      "resourceId": 1,
      "parameter": {
        "hdfsDir": "hdfs://localhost/daily/t_test/ds=${yyyyMMdd}",
        "columns": [
          "id",
          "Dir_Ahead",
          "Dir_Area_Num",
          "Dir_Department"
        ]
      },
      "where": "id=1"
    },
    {
      "type": "mysql",
      "class": "com.horse.rw.plugins.mysql.MySQLReader",
      "resourceId": 2,
      "parameter": {
        "tableName": "data_directory_v1",
        "columns": [
          "id",
          "Dir_Ahead",
          "Dir_Area_Num",
          "Dir_Department"
        ]
      },
      "where": "id=2"
    },
    {
      "type": "mysql",
      "class": "com.horse.rw.plugins.mysql.MySQLReader",
      "resourceId": 3,
      "parameter": {
        "tableName": "data_directory_v2",
        "columns": [
          "id",
          "Dir_Ahead",
          "Dir_Area_Num",
          "Dir_Department"
        ]
      },
      "where": "id=3"
    },
    {
      "type": "...",
      "class": "....",
      "resourceId": 4,
      "...": "..."
    }
  ]
}
```
###### e.g Writer.Json配置如下，整体结构为：{"writer":[{},{},{},...]}
```Json

```

### 数据源输入输出列表

| Data source input type | Data source output type |   support  |  
| ---------------------- | :---------------------- | :--------: |
|      ***MySQL***       |       ***MySQL***       |     Yes    |
|      ***MongDB***      |       ***MongDB***      |     Yes    |
|      ***Oracle***      |       ***Oracle***      |     No     |
|      ***HDFS***        |       ***HDFS***        |     Yes    |
|      ***Kudu***        |       ***Kudu***        |     Yes    |
|      ***Hive***        |       ***Hive***        |     Yes    |
|      ***ODPS***        |       ***ODPS***        |     Yes    |
|  ***ElasticSearch***   |  ***ElasticSearch***    |     Yes    |
|     ***Kafka***        |      ***Kafka***        |     Yes    |
|    ***SQLServer***     |     ***SQLServer***     |     No     |


### 联系方式（欢迎一起交流学习如何更加完善此项目）：
- 邮箱：HoneyHorseGithub@163.com
- QQ群：
- 微信群：
