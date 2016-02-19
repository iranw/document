### Logstash脚本demo

###### 命令行启动
```
$ ./bin/logstash -e 'input{stdin{}}output{stdout{codec=>rubydebug}}'
```

###### 通过redis这个broker方式将数据导入到es服务器
```
input {
    redis {
        batch_count => 1
        data_type => "list"
        key => "logstash-list"
        host => "192.168.56.50"
        port => 6379
        threads => 5
    }
}
output{
    elasticsearch {
        hosts => ["192.168.56.101:9200"]
        index => "goods_pool_%{id}"
        document_type => "goods"
        document_id => "%{id}"              #设定特定值为_id
    }
}
```

###### 通过redis导入es服务器
```
input {
    redis {
        batch_count => 1
        data_type => "list"
        key => "logstash-list"
        host => "192.168.56.50"
        port => 6379 
        threads => 5
    }
}
output{
    if [document_id] {
        elasticsearch {
            hosts => ["192.168.200.198:9270"]
            index => "goodspool_dev"
            document_type => "goods"
            document_id => "%{document_id}"
        }
    } else {
        elasticsearch {
            hosts => ["192.168.200.198:9270"]
            index => "goodspool_dev"
            document_type => "goods"
            flush_size => 500
        }
    }
}
```

###### collectd性能数据收集脚本(Logstash配置)
```
input {
    udp {
        port => 25826
        buffer_size => 1452
        workers => 3          # Default is 2
        queue_size => 30000   # Default is 2000
        codec => collectd { }
        type => "collectd"
    }
}
output{
    stdout{
        codec=>rubydebug
    }
}
```
###### collectd性能数据收集脚本(collectd配置)
```
Hostname    "host.example.com"
LoadPlugin interface
LoadPlugin load
LoadPlugin memory
LoadPlugin network
<Plugin interface>
    Interface "eth0"
    IgnoreSelected false
</Plugin>
<Plugin network>
    <Server "10.0.0.1" "25826">
    </Server>
</Plugin>
```
`注`: [https://www.elastic.co/guide/en/logstash/current/plugins-codecs-collectd.html](https://www.elastic.co/guide/en/logstash/current/plugins-codecs-collectd.html)  [https://gist.github.com/untergeek/ab85cb86a9bf39f1fc6d](https://gist.github.com/untergeek/ab85cb86a9bf39f1fc6d)

###### 读取日志文件（json日志文件）
```
input {
    file {
        type => "json-log"
        path => ["/data/win/ivan/logs/*.log"]
        start_position => "beginning"
        codec => "json"
    }
}
output{
    stdout{
        codec=>rubydebug
    }
}
```


###### 标准输入日志文件
```
input {
    stdin {
        add_field => {"key" => "value"}
        codec => "plain"
        tags => ["add"]
        type => "std"
    }
}
output{
    stdout{
        codec=>rubydebug
    }
}
```