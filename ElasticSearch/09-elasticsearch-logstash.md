> ## elasticsearch logstash

* [logstash 官方文档](https://www.elastic.co/guide/en/logstash/current/index.html)
* [logstash kafka 官方配置文档](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-kafka.html)
* [logstash mysql 同步 elasticsearch 官方文档](https://www.elastic.co/cn/blog/how-to-keep-elasticsearch-synchronized-with-a-relational-database-using-logstash)

Logstash事件处理管道有三个阶段：输入 > 过滤器 > 输出。

> 下载 logstash 解压

```shell
# 运行指令 --config.reload.automatic 配置自动重新加载
bin/logstash.bat -f config/elastic.conf
```

> logstash 的脚本语法

* `date` : 日期解析
* `grok` : 正则匹配解析
* `dissect` : 分割符解析
* `mutate` : 对字段进行处理(增删改)
* `json` : 按照 json 解析字段内容到指定字段中
* `geoip` : 增加地理位置数据
* `ruby` : 利用 ruby 代码来动态修改 logstash event

---

* elastic.conf 同步es索引数据

```json
input {
    elasticsearch {
        hosts => ["localhost:9201"]
        index => "data_index"
        query => '{ "query": {"match_all" : {} } }'
        size => 1000
        scroll => "1m"
        codec => "json"
        docinfo => true
    }
}

output {
    stdout {
      codec => rubydebug
    }
    elasticsearch {
        hosts => ["localhost:9200"]
        index => "data_index"
        document_type => "data_info"
        document_id => "%{id}" 
    }
}
```

* mysql.conf MySQL同步数据到es

```json
input {
  jdbc {
    jdbc_driver_library => "D:\maven\repository\mysql\mysql-connector-java\5.1.38\mysql-connector-java-5.1.38.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    jdbc_connection_string => "jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8&useSSL=true&serverTimezone=GMT%2B8"
    jdbc_user => "root"
	jdbc_password => "123456"
	jdbc_paging_enabled => "true"
	jdbc_page_size => "10"
    statement => "select * from person_info"
    schedule => "* * * * *"
    last_run_metadata_path => "D:\testEvn\logstash-6.2.2\config\logstash_metadata"
  }
}

output {
 elasticsearch {
	 hosts => "localhost:9201"
	 index => "lg_person"
	 document_id => "%{id}"
	 document_type => "person_info"
	}
}
```

* csv.conf csv 数据同步到 es

```json
input
{
    file{
	# path => ["/文件的绝对路径/Reviews.csv"] 
	start_position => "beginning"
    }
}
filter{
    csv{
        separator => ","
	     columns => ["Id","ProductId","UserId","ProfileName","HelpfulnessNumerator","HelpfulnessDenominator","Score","Time","Summary","Text"]
       }
    mutate{
	    convert => {
	        "Id" => "integer"
	        "ProductId" => "string"
	        "ProfileName" => "string"
	        "HelpfulnessNumerator" => "integer"
	        "HelpfulnessDenominator" => "integer"
	        "score" => "integer"
	        "Time" => "integer"
	        "Summary" => "string"
	        "Text" => "string"
	}
    }
}
output{
	if [input_type] == 'aaa'{
		 elasticsearch{
		    hosts => ["localhost:9201"]
		    index => "food_reviews"
  		  }
	}else if [input_type] == 'bbb'{
		 elasticsearch{
		    hosts => ["localhost:9201"]
		    index => "food_reviews"
  		  }
	}else{
		 elasticsearch{
		    hosts => ["localhost:9201"]
		    index => "food_reviews"
  		  }
	}
   
}
```

* input kafka output db
* `bin/logstash-plugin install logstash-output-jdbc`
* 离线安装 `logstash-output-jdbc` https://blog.csdn.net/u011311291/article/details/86743602

```json
input {

  kafka {
    bootstrap_servers => "192.168.0.1:9092,192.168.0.1:9092,192.168.0.1:9092"
    topics => ["data_10086"]
    group_id => "logstash_group"
    consumer_threads => 1
    codec => "json"
    type => "10086"
  }

  kafka {
    bootstrap_servers => "192.168.0.1:9092,192.168.0.1:9092,192.168.0.1:9092"
    topics => ["data_10010"]
    group_id => "logstash_group"
    consumer_threads => 1
    codec => "json"
    type => "10010"
 }

}

filter {
  json {
    source => "message"
  }
}

output {

  if [type] == "10086" {
    if [sql_model] == "INSERT" {
      jdbc {
        driver_jar_path => "/data/soft/driver/mysql-connector-java-8.0.27.jar"
        driver_class => "com.mysql.cj.jdbc.Driver"
        connection_string => "jdbc:mysql://192.168.1.1:3306/db_name?characterEncoding=utf-8&useSSL=false&serverTimezone=GMT%2B8&zeroDateTimeBehavior=convertToNull&user=root&password=123456"
        statement => [ "INSERT INTO `tbl_xxx` (`id`,`phone`) VALUES (?,?);", "id","phone" ]
      }
    }

     if [sql_model] == "UPDATE" {
      jdbc {
        driver_jar_path => "/data/soft/driver/mysql-connector-java-8.0.27.jar"
        driver_class => "com.mysql.cj.jdbc.Driver"
        connection_string => "jdbc:mysql://192.168.1.1:3306/db_name?characterEncoding=utf-8&useSSL=false&serverTimezone=GMT%2B8&zeroDateTimeBehavior=convertToNull&user=root&password=123456"
        statement => [ "UPDATE `tbl_xxx` SET `phone` = ? WHERE `id` = ?;", "phone","id" ]
      }
    }
  }
  
  if [type] == "10010" {
    if [sql_model] == "INSERT" {
      jdbc {
        driver_jar_path => "/data/soft/driver/ojdbc8-19.13.0.0.0.jar"
        driver_class => "oracle.jdbc.driver.OracleDriver"
        connection_string => "jdbc:oracle:thin:root/123456@192.168.2.1:1621/db_name"
        statement => [ 'INSERT INTO "tbl_xxx"("id","phone") VALUES (?, ?) ', "id","phone" ]
      }
    }
	
    if [sql_model] == "UPDATE" {
      jdbc {
        driver_jar_path => "/data/soft/driver/ojdbc8-19.13.0.0.0.jar"
        driver_class => "oracle.jdbc.driver.OracleDriver"
        connection_string => "jdbc:oracle:thin:root/123456@192.168.2.1:1621/db_name"
        statement => [ 'UPDATE "tbl_xxx" SET "phone" = ? WHERE "id" = ? ', "%{phone}","id" ]
      }
    }
  }

}
```


> 验证 ElasticSearch X-pack

* 编辑 `config/logstash.yml` 文件

```yaml
#xpack.monitoring.elasticsearch.username: logstash_system
#xpack.monitoring.elasticsearch.password: password
```
