###### 全文匹配
```
POST /test/_search
{
    "query": { 
        "match_all": {} 
    }
}
```


###### 聚合demo
```
POST /cars/transactions/_search?search_type=count
{
    "from" : 0, 
    "size" : 3,
    "aggs" : { 
        "colors" : { 
            "terms" : {
              "field" : "color" 
            },
            "aggs": { 
                "avg_price": { 
                   "avg": {
                      "field": "price" 
                   }
                }
             }
        }
    }
}
```

###### 聚合demo 
```
POST /cars/transactions/_search?search_type=count
{
    "from" : 0, 
    "size" : 3,
    "aggs" : { 
        "colors" : { 
            "terms" : {
              "field" : "color" 
            }
        }
    }
}
```

###### 聚合demo
```
POST /cars/transactions/_search?search_type=count
{
    "from" : 0, 
    "size" : 3,
    "aggs" : { 
        "colors" : { 
            "terms" : {
              "field" : "color" 
            },
            "aggs": { 
                "avg_price": { 
                   "avg": {
                      "field": "price" 
                   }
                },
                "make": { 
                    "terms": {
                        "field": "make" 
                    }
                }
             }
        }
    }
}
```

###### 范围查询 range
```
POST /ptest/goods/_search
{
    "query": {        
        "filtered": {
            "query":    { "match_all": {}},
            "filter":   {
                "range" : {
                    "age" : {
                        "gte" : 25,
                        "lt" :  30
                    }
                }
            }
        }
    }
}
```

###### filter+sort
```
POST /goodspool/_search
{
    "from" : 0, 
    "size" : 100,
    "query": {        
        "filtered": {
            "query":    { "match_all": {}},
            "filter":   { 
                "terms": { "province_id": [8,7] }
            }
        }
    },
    "sort" : [
        {
            "uv" : {"order" : "desc"}
        }
    ]
}
```

```
POST /goodspool/_search
{
    "from" : 0, 
    "size" : 100,
    "query": {        
        "bool" : {
            "must" : [
                {
                    "terms":{"province_id":[8]}
                }
            ]
        }
    }
}
```

```
POST /goodspool/_search
{
    "from" : 0, 
    "size" : 100,
    "query": {        
        "bool" : {
            "must" : [
                {
                    "match":{"province_id":"9"}
                }
            ]
        }
    }
}
```


###### 过滤+排序
```
POST /ptest/goods/_search
{
    "from" : 0, 
    "size" : 100,
    "query": {        
        "filtered": {
            "query":    { "match_all": {}},
            "filter":   {
                "bool" : {
                    "must":[
                        {
                            "terms" : {"age" : [22,23,25,24]}
                        }
                    ]
                }
            }
        }
    },
    "sort" : [
        {
            "age" : {"order" : "asc"}
        }
    ]
}
```

######　multi query
```
POST /ptest/goods/_search
{
    "from" : 0, 
    "size" : 100,
    "query": {        
        "bool" : {
            "must" : [
                {
                    "terms":{"age":[22]}
                },
                {
                    "term":{"name":"iranw"}
                }
            ]
        }
    }
}
```

###### 
```
POST /ptest/goods/_search
{
    "from" : 0, 
    "size" : 100,
    "query": {        
        "bool" : {
            "must" : [
                {
                    "terms":{"age":[22]}
                },
                {
                    "match":{"name":"iranw"}
                }
            ]
        }
    }
}
```

```
POST /ptest/goods/_search
{
    "from" : 0, 
    "size" : 100,
    "query": {        
        "bool" : {
            "must" : {
                "terms":{"age":[22,25]}
            }
        }
    }
}
```

###### 全文搜索
```
POST /ptest/_search
{
    "from": 0,
    "size": 15,
    "query": {
        "match": {
            "_all": "核心编程"
        }
    }
}
```

###### 特定字段分词验证
```
POST /ptest/_search
{
    "from": 0,
    "size": 15,
    "query": {
        "match": {
            "_all": "核心编程"
        }
    }
}
```


