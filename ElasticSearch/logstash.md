
### 通过redis这个broker方式将数据导入到es服务器
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


