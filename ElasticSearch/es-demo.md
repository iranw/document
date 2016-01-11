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



