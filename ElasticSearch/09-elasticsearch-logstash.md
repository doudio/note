> ## elasticsearch logstash

Logstash事件处理管道有三个阶段：输入 > 过滤器 > 输出。

> 下载 logstash 解压

```shell
# 运行指令
bin/logstash.bat -f config/elastic.conf
```

> logstash 的脚本语法

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

> 验证 ElasticSearch X-pack

* 编辑 `config/logstash.yml` 文件

```yaml
#xpack.monitoring.elasticsearch.username: logstash_system
#xpack.monitoring.elasticsearch.password: password
```
