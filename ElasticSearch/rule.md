
### 搜索规则

1. 根据商品分类进行筛选
2. 根据地区进行筛选
3. 根据特定时间区域筛选
4. 根据商品种类进行筛选
5. 根据指标进行排序


###### demo1（根据商品种类进行筛选）
```
POST /goodspool_dev/_search
{
    "from" : 0, 
    "size" : 20,
    "query": {        
        "filtered": {
            "query":    { "match_all": {}},
            "filter":   { 
                "term": { "data_type": "图书 " }
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
POST /goodspool_dev/_search
{
    "from" : 0, 
    "size" : 20,
    "query": {        
        "filtered": {
            "query":    { "match_all": {}},
            "filter":   { 
                "terms": { "data_type": ["图书 ","pop "] }
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

###### demo2(筛选最近7天的数据)
```
POST /goodspool_dev/_search
{
    "from" : 0, 
    "size" : 20,
    "query": {        
        "filtered": {
            "query":    { "match_all": {}},
            "filter":   { 
                "range": {
                    "dt": {
                        "gte" : "now-7d/d"                   
                    }
                }
            }
        }
    }
}
```

###### demo3 (多级筛选/日期&种类混合筛选)
```
POST /goodspool_dev/_search
{
    "from" : 0, 
    "size" : 20,
    "query": {        
        "filtered": {
            "query":    { "match_all": {}},
            "filter":   { 
                "bool" : {
                    "must" : [
                        {
                            "range": {
                                "dt": {
                                    "gte" : "now-7d/d"                   
                                }
                            }
                        },
                        {
                            "term": {
                               "data_type": "图书 "
                            }
                        }
                    ]
                }
            }
        }
    }
}
```

###### demo4 (多重筛选+排序)
```
POST /goodspool_dev/_search
{
    "from" : 0, 
    "size" : 20,
    "query": {        
        "filtered": {
            "query":    { "match_all": {}},
            "filter":   { 
                "bool" : {
                    "must" : [
                        {
                            "range": {
                                "dt": {
                                    "gte" : "now-7d/d"                   
                                }
                            }
                        },
                        {
                            "term": {
                               "data_type": "图书 "
                            }
                        }
                    ]
                }
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

###### demo5（多重筛选+排序）
```
POST /goodspool_dev/_search
{
    "from" : 0, 
    "size" : 20,
    "query": {        
        "filtered": {
            "query":    { "match_all": {}},
            "filter":   { 
                "bool" : {
                    "must" : [
                        {
                            "range": {
                                "dt": {
                                    "gte" : "now-7d/d"                   
                                }
                            }
                        },
                        {
                            "term": {
                               "data_type": "图书 "
                            }
                        },
                        {
                            "terms": {
                               "province_id": [8,9]
                            }
                        },
                        {
                            "term": {
                               "item_third_cate_cd": 3333
                            }
                        }
                    ]
                }
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

###### demo6 (自定义排序)
`注`：记得加配置选项`script.engine.groovy.inline.search: on`
```
POST /goodspool_dev/_search
{
    "from" : 0, 
    "size" : 20,
    "query": {        
        "filtered": {
            "query":    { "match_all": {}},
            "filter":   { 
                "terms": { "data_type": ["图书 ","pop "] }
            }
        }
    },
    "sort" : {
        "_script" : {
            "type" : "number",
            "script" : "doc['sale_qtty'].value / doc['visits'].value",
            "order" : "desc"
        }
    }
}
```

```
POST /ptest/book/_search
{
    "query": {        
        "filtered": {
            "query":    { "match_all": {}}
        }
    },
    "sort" : {
        "_script" : {
            "type" : "number",
            "script" : "doc['uv'].value / doc['num'].value",
            "order" : "desc"
        }
    }
}
```