
### ES小记

###### 1、查看服务器信息
```
curl 'http://localhost:9200/?pretty'
{
  "name" : "Tattoo",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "2.1.1",
    "build_hash" : "40e2c53a6b6c2972b3d13846e450e66f4375bd71",
    "build_timestamp" : "2015-12-15T13:05:55Z",
    "build_snapshot" : false,
    "lucene_version" : "5.3.1"
  },
  "tagline" : "You Know, for Search"
}
```

2、节点(node)是一个运行着的Elasticsearch实例。集群(cluster)是一组具有相同cluster.name的节点集合，当然一个节点也可以组成一个集群。

3、配置文件config/elasticsearch.yml 的cluster.name 设置集群名称

4、es请求格式
curl -X<VERB> '<PROTOCOL>://<HOST>/<PATH>?<QUERY_STRING>' -d '<BODY>'
VERB HTTP方法：GET, POST, PUT, HEAD, DELETE
BODY 一个JSON格式的请求主体（如果请求需要的话）

5、计算集群中文件数量 
curl -XGET 'http://localhost:9200/_count?pretty' -d '
{
    "query": {
        "match_all": {}
    }
}
'

6、curl命令后跟-i参数:可以返回header头

7、在Elasticsearch中，文档归属于一种类型(type),而这些类型存在于索引(index)中
Relational DB -> Databases -> Tables -> Rows    -> Columns
Elasticsearch -> Indices   -> Types  -> Documents -> Fields
Elasticsearch集群可以包含多个索引(indices)（数据库），每一个索引可以包含多个类型(types)（表），每一个类型包含多个文档(documents)（行），然后每个文档包含多个字段(Fields)（列）。

８、插入或者更新数据put
/索引(数据库)/类型(类型)/文档(行)
curl -XPUT 'http://localhost:9200/megacorp/employee/1' -d '
{
    "first_name" : "John",
    "last_name" :  "Smith",
    "age" :        25,
    "about" :      "I love to go rock climbing2",
    "interests": [ "sports", "music" ]
}
'

9、获取数据get
curl -XGET 'http://localhost:9200/megacorp/employee/1?pretty=1'
{
  "_index" : "megacorp",
  "_type" : "employee",
  "_id" : "1",
  "_version" : 3,
  "found" : true,
  "_source":
    {
            "first_name" : "John",
            "last_name" :  "Smith",
            "age" :        25,
            "about" :      "I love to go rock climbing2",
            "interests": [ "sports", "music" ]
    }
}

10、简单搜索
搜索特定索引特定类型的全部结果
curl -XGET 'http://localhost:9200/megacorp/employee/_search?pretty=1'
搜索特定字段含有特定字符串的结果
curl -XGET '/megacorp/employee/_search?q=last_name:s&pretty=1'
DSL查询
curl -XGET 'http://localhost:9200/megacorp/employee/_search?pretty=1' -d '
{
    "query" : {
        "match" : {
            "last_name" : "Smith"
        }
    }
}
'
过滤器
curl -XGET 'http://localhost:9200/megacorp/employee/_search?pretty=1' -d '
{
    "query" : {
        "filtered" : {
            "filter" : {
                "range" : {
                    "age" : { "gt" : 20 } 
                }
            },
            "query" : {
                "match" : {
                    "last_name" : "smith" 
                }
            }
        }
    }
}
'
于区间过滤器(range filter)
短语搜索：使用match_phrase 代替match
curl -XGET 'http://localhost:9200/megacorp/employee/_search?pretty=1' -d '
{
    "query" : {
        "match_phrase" : {
            "last_name" : "Smith"
        }
    }
}
'
高亮搜索
curl -XGET 'http://localhost:9200/megacorp/employee/_search' -d '
{
    "query" : {
        "match" : {
            "about" : "rock"
        }
    },
    "highlight": {
        "fields" : {
            "about" : {}
        }
    }
}
'
聚合(aggregations)查询
curl -XGET 'http://localhost:9200/megacorp/employee/_search' -d '
{
  "aggs": {
    "all_interests": {
      "terms": { "field": "interests" }
    }
  }
}
'

集群(cluster)、节点(node)和分片(shard)
纵向扩展(vertical scale or scaling up)+ 横向扩展(horizontal scale or scaling out)

一个节点(node)就是一个Elasticsearch实例，而一个集群(cluster)由一个或多个节点组成，它们具有相同的cluster.name

集群中一个节点会被选举为主节点(master),它将临时管理集群级别的一些变更，例如新建或删除索引、增加或移除节点等。主节点不参与文档级别的变更或搜索，这意味着在流量增长的时候，该主节点不会成为集群的瓶颈。任何节点都可以成为主节点。

查看集群监控状况
curl -XGET 'http://localhost:9200/_cluster/health?pretty=1'
{
  "cluster_name" : "test001",
  "status" : "red",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 6,
  "active_shards" : 6,
  "relocating_shards" : 0,
  "initializing_shards" : 1,
  "unassigned_shards" : 7,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 42.857142857142854
}
status字段提供一个综合的指标来表示集群的的服务状况。三种颜色各自的含义：
颜色  意义
green   所有主要分片和复制分片都可用
yellow  所有主要分片可用，但不是所有复制分片都可用
red 不是所有的主要分片都可用


主要分片(primary shard)和复制分片(replica shard)

为了将数据添加到Elasticsearch，我们需要索引(index)——一个存储关联数据的地方。实际上，索引只是一个用来指向一个或多个分片(shards)的“逻辑命名空间(logical namespace)”.

分片就是一个Lucene实例，并且它本身就是一个完整的搜索引擎

分片可以是主分片(primary shard)或者是复制分片(replica shard)。你索引中的每个文档属于一个单独的主分片，所以主分片的数量决定了索引最多能存储多少数据。

复制分片只是主分片的一个副本，它可以防止硬件故障导致的数据丢失，同时可以提供读请求，比如搜索或者从别的shard取回文档。

当索引创建完成的时候，主分片的数量就固定了，但是复制分片的数量可以随时调整。

在es中，每一个字段的数据都是默认被索引的

键(key)是字段(field)或属性(property)的名字，值(value)可以是字符串、数字、布尔类型、另一个对象、值数组或者其他特殊类型

通常，我们可以认为对象(object)和文档(document)是等价相通的

通常，我们可以认为对象(object)和文档(document)是等价相通的。不过，他们还是有所差别：对象(Object)是一个JSON结构体——类似于哈希、hashmap、字典或者关联数组；对象(Object)中还可能包含其他对象(Object)。 在Elasticsearch中，文档(document)这个术语有着特殊含义。它特指最顶层结构或者根对象(root object)序列化成的JSON数据（以唯一ID标识并存储于Elasticsearch中）。

一个文档不只有数据。它还包含了元数据(metadata)——关于文档的信息

节点  说明
_index  文档存储的地方
_type   文档代表的对象的类
_id 文档的唯一标识

索引(index)类似于关系型数据库里的“数据库”——它是我们存储和索引关联数据的地方。

事实上，我们的数据被存储和索引在分片(shards)中，索引只是一个把一个或多个分片分组在一起的逻辑空间。

索引名必须小写，不能以下划线开头，不能包含逗号。

每个类型(type)都有自己的映射(mapping)或者结构定义，就像传统数据库表中的列一样。

_type的名字可以是大写或小写，不能包含下划线或逗号

Elasticsearch中每个文档都有版本号，每当文档变化（包括删除）都会使_version增加

注入数据，自定id
curl -XPUT "http://192.168.56.50:9200/website/blog/2" -d '
{
  "title": "22My first blog entry",
  "text":  "2Just trying this out...",
  "date":  "22014/01/01"
}'

注入数据，自动生成id
curl -XPOST "http://192.168.56.50:9200/website/blog/" -d '
{
  "title": "My second blog entry",
  "text":  "Still trying this out...",
  "date":  "2014/01/01"
}'
自动生成的ID有22个字符长

获取文档
curl –XGET "http://192.168.56.50:9200/website/blog/1?pretty=1"
pretty参数用户美化

只获取source字段
curl -i -XGET "http://192.168.56.50:9200/website/blog/1/_source?pretty=1"

curl后加-i参数得到响应头

检查文档是否存在
curl -i -XHEAD 'http://192.168.56.50:9200/website/blog/222'
返回200说明存在 404不存在

更新文档
curl -XPUT "http://192.168.56.50:9200/website/blog/1" -d '
{
  "title": "update My first blog entry",
  "text":  "I am starting to get the hang of this...",
  "date":  "2014/01/02"
}'
更新后_version的值增加了，并且created值为1
内部炒作为，老文档已经标记为旧文档，并且新添加一个新文档
1.  从旧文档中检索JSON
2.  修改它
3.  删除旧文档
4.  索引新文档

强制添加一个新文档
curl -i -XPUT "http://192.168.56.50:9200/website/blog/100?op_type=create" -d '
{
  "title": "11111111111111My first blog entry",
  "text":  "I am starting to get the hang of this...",
  "date":  "2014/01/02"
}'
或者
curl -i -XPUT "http://192.168.56.50:9200/website/blog/100/_create" -d '
{
  "title": "11111111111111My first blog entry",
  "text":  "I am starting to get the hang of this...",
  "date":  "2014/01/02"
}'
创建成功返回201 失败返回409
另外最简单的强制插入数据是post数据让es自动生成唯一id

删除文档
curl -XDELETE "http://192.168.56.50:9200/website/blog/100"
如果删除的文档未找到，返回404并且相应体found 为false

悲观并发控制（关系型数据库）
乐观并发控制（es使用）

并发控制处理（设置特定更新的版本，这样就可以避免冲突更新）
curl -XPUT "http://192.168.56.50:9200/website/blog/1?version=4&pretty" -d '
{
  "title": "11111111111111My first blog entry",
  "text":  "I am starting to get the hang of this...",
  "date":  "2014/01/02"
}'
所有的更新和删除文档请求都接受version参数，在代码中手动添加乐观并发锁(?version=xxx)
Version_type可以使用外部版本号，根据记录的时间戳来保证乐观并发锁version_type=external

文档局部更新
curl -XPOST "http://192.168.56.50:9200/website/blog/1/_update?pretty" -d '
{
   "doc" : {
      "tags" : [ "testing" ],
      "views": 0
   }
}'
使用_update标识

使用脚本更新：暂时不表

当更新不存在的文档时会失败，但是如果加上upsert参数来使其不存在时创建

更新的时候可以添加retry_on_conflict参数来设置重置次数来自动完成更新
POST /website/pageviews/1/_update?retry_on_conflict=5 <1>
{
   "script" : "ctx._source.views+=1",
   "upsert": {
       "views": 0
   }
}

检索多个文档
curl -XGET "http://192.168.56.50:9200/_mget?pretty=1" -d '
{
   "docs" : [
      {
         "_index" : "website",
         "_type" :  "blog",
         "_id" :    1
      },
      {
         "_index" : "website",
         "_type" :  "pageviews",
         "_id" :    1,
         "_source": "views"
      }
   ]
}'
特定类型检索
curl -XGET "http://192.168.56.50:9200/website/blog/_mget?pretty=1" -d '
{
   "docs" : [
      { "_id" : 2 },
      { "_type" : "pageviews", "_id" :   1 }
   ]
}'

curl -XGET "http://192.168.56.50:9200/website/blog/_mget?pretty=1" -d '
{
   "ids":[1,2,3]
}'

_bulk类似于批量操作，减少网络请求量

==========================================================================================================================================================================================

所有的文档API（get、index、delete、bulk、update、mget）都接收一个routing参数，它用来自定义文档到分片的映射。自定义路由值可以确保所有相关文档被保存在一个分片上。

Replication
复制的默认值是sync，这将导致主分片得到的复制分片的成功响应后才返回
如果设置replication为async，请求在主分片上被执行后就会返回给客户端。(不建议使用async)


==============================================================
============================搜  索==============================
==============================================================

Es中每个文档的字段都会被索引并被查询

概念  解释
映射(Mapping) 数据在每个字段中的解释说明
分析(Analysis)    全文是如何处理的可以被搜索的
领域特定语言查询(Query DSL) Elasticsearch使用的灵活的、强大的查询语言


获取索引列表
curl http://192.168.56.50:9200/_cat/indices?v

删除索引
curl -XDELETE "http://192.168.56.50:9200/website/"

空搜索
curl -XGET http://localhost:9200/_search
curl -XGET http://localhost:9200/us/_search

curl -XGET http://localhost:9200/us/_search?timeout=10ms
timeout不会停止查询，它仅仅告诉你目前顺利返回结果的节点然后关闭连接. 在后台，其他分片可能依旧执行查询，尽管结果已经被发送。

搜索返回结果
Name    解释
Total   匹配到的文件总数
Hits数组  匹配到的前十条结果
_score  相关性得分
max_score   所有匹配文档中的_score的最大值
Took    整个搜索请求花费的毫秒数
shards
    参与查询的分片数（total字段）
timed_out   告诉我们查询超时与否

在所有索引的所有类型中搜索
curl -XGET http://localhost:9200/_search?timeout=10ms

在索引gb的所有类型中搜索
curl -XGET http://localhost:9200/gb/_search?timeout=10ms

在索引gb和us的所有类型中搜索
curl -XGET http://localhost:9200/gb,us/_search?timeout=10ms

在以g或u开头的索引的所有类型中搜索
curl -XGET http://localhost:9200/g*,u*/_search?timeout=10ms

在索引gb的类型user中搜索
curl -XGET http://localhost:9200/gb/user/_search?timeout=10ms

在索引gb和us的类型为user和tweet中搜索
curl -XGET http://localhost:9200/gb,us/user,tweet/_search?timeout=10ms

在所有索引的user和tweet中搜索
curl -XGET http://localhost:9200/_all/user,tweet/_search?timeout=10ms

搜索一个索引有5个主分片和5个索引各有一个分片事实上是一样的。

Size：结果数，默认为10
From：跳过开始的结果数，默认0
页码1-3的请求，每页5个结果
curl -XGET http://localhost:9200/_search?size=5
curl -XGET http://192.168.56.50:9200/_search?size=5&from=5 
curl -XGET http://192.168.56.50:9200/_search?size=5&from=10

分布系统中，排序结果的花费随着分页的深入而成倍的增长

search API有两种表单：一种是“简易版”的查询字符串(query string)将所有参数通过查询字符串定义，另一种版本使用JSON完整的表示请求体(request body)，这种富搜索语言叫做结构化查询语句（DSL）

查询所有类型为tweet并在tweet字段中包含elasticsearch字符的文档
curl -XGET http://xxx:9200/_all/tweet/_search?q=tweet:elasticsearch
下一个语句查找name字段中包含"john"和tweet字段包含"mary"的结果
curl -XGET http://192.168.56.50:9200/_search?q=+name:john+tweet:mary

返回包含"mary"字符的所有文档的简单搜索
curl -XGET http://192.168.56.50:9200/_search?q=mary

Elasticsearch把所有字符串字段值连接起来放在一个大字符串中，它被索引为一个特殊的字段_all

当_all字段不再使用，你可以停用它



==============================================================
==============================================================
============================映  射==============================
==============================================================
==============================================================

映射(mapping)机制用于进行字段类型确认，将每个字段匹配为一种确定的数据类型(string, number,booleans, date等)。

分析(analysis)机制用于进行全文文本(Full Text)的分词，以建立供搜索用的反向索引。

而对于全文数据的查询来说，却有些微妙。我们不会去询问这篇文档是否匹配查询要求？。 但是，我们会询问这篇文档和查询的匹配程度如何？。换句话说，对于查询条件，这篇文档的相关性有多高？

Elasticsearch使用一种叫做倒排索引(inverted index)的结构来做快速的全文搜索

测试分析器，通过analyze API来查询词是如何被分析的
curl -XGET "http://localhost:9200/_analyze?analyzer=standard&pretty=1" -d '
Text to analyze
'

Elasticsearch支持以下简单字段类型：
类型  表示的数据类型
String  string
Whole number    byte, short, integer, long
Floating point  float, double
Boolean boolean
Date    date

index参数控制字符串以何种方式被索引
值   解释
analyzed    首先分析这个字符串，然后索引。换言之，以全文形式索引此字段。
not_analyzed    索引这个字段，使之可以被搜索，但是索引内容和指定值一样。不分析此字段。
no  不索引这个字段。这个字段不能为搜索到。

初始化映射
curl -XPUT "http://localhost:9200/test/" -d '
{
  "mappings": {
    "tweet" : {
      "properties" : {
        "tweet" : {
          "type" :    "string",
          "analyzer": "english"
        },
        "date" : {
          "type" :   "date"
        },
        "name" : {
          "type" :   "string"
        },
        "user_id" : {
          "type" :   "long"
        }
      }
    }
  }
}
'

增加映射
curl -XPUT "http://localhost:9200/test/_mapping/tweet" -d ' 
{
  "properties" : {
    "tag" : {
      "type" :    "string",
      "index":    "not_analyzed"
    }
  }
}
'

测试映射（测试有分词的字段）
curl -XGET "http://localhost:9200/test/_analyze?field=tweet" -d '
Black-cats
'
测试映射（测试无分词的字段）
curl -XGET "http://localhost:9200/test/_analyze?field=tag" -d '
Black-cats
'

除了之前提到的简单的标量类型，JSON还有null值，数组和对象，所有这些Elasticsearch都支持：

多值字段(数组类型，数组中的所有值必须为同一类型)
{ "tag": [ "search", "nosql" ]}

如果你创建一个新字段，这个字段索引了一个数组，Elasticsearch将使用第一个值的类型来确定这个新字段的类型。




==============================================================
==============================================================
==========================结  构  化  查  询========================
==============================================================
==============================================================

空查询，查询所有的文档
curl -XGET "http://localhost:9200/_search?pretty=1" -d '
{}
'

查询特定索引，特定类型的文档
curl -XGET "http://localhost:9200/index_*/type1,type2/_search?pretty=1" -d '
{}
'

分页查询
curl -XGET "http://localhost:9200/_search?pretty=1" -d '
{
"from": 30,
  "size": 10
}
'

Search API不但支持GET请求，还支持POST请求

使用结构化查询，需要传递query参数

空查询等同于match_all查询子句
curl -XGET "http://localhost:9200/_search?pretty=1" -d '
{
"query": {
        "match_all": {}
    }
}
'

查询特定字段包含特定字符串的结果(tweet类型字段含有elasticsearch字符串的结果)
curl -XGET "http://localhost:9200/_search?pretty=1" -d '
{
"query": {
        "match": {
            "tweet": "elasticsearch"
        }
    }
}
'

特定条件满足（同时必须包含和不必须包含）
curl -XGET "http://192.168.56.50:9200/us/_search?pretty=1" -d '
{
    "query": {
        "bool": {
        "must": {"match":{"tweet":"Elasticsearch"}},
            "must_not": {"match":{"tweet":"API"}}
        }
    }
}
'

ES我们可以用两种结构化语句：结构化查询(Query DSL)&结构化过滤(Filter DSL)

一般来说，查询语句要比过滤语句更耗时

布尔查询
字段  解释
Must    此查询必须出现在匹配文档中，有助于搜索结果分数_score。
Filter  此查询必须出现在匹配文档中，但不会影响_score。
Should  至少有一个查询条件匹配, 相当于 or。
Must_not    不能出现在匹配文档中

term主要用于精确匹配哪些值，比如数字，日期，布尔值或 not_analyzed的字符串(未经分析的文本数据类型)：
{ "term": { "age":    26           }}
{ "term": { "date":   "2014-09-01" }}
{ "term": { "public": true         }}
{ "term": { "tag":    "full_text"  }}

做精确匹配搜索时，你最好用过滤语句，因为过滤语句可以缓存数据。

验证api，验证查询语句是否合法
curl -XGET "http://192.168.56.50:9200/us/_validate/query" -d '
{
    "query" : {
    }
}
'

显示错误信息的详细信息
curl -XGET "http://192.168.56.50:9200/us/_validate/query?explain&pretty=1" -d '
{
    "query" : {
    }
}
'

字段值默认以顺序排列，而 _score 默认以倒序排列。

排序
curl -XGET " http://192.168.56.50:9200/_search" -d '
{
    "query" : {
        "filtered" : {
            "filter" : {
                "term" : {
                    "name" : "john"
                }
            }
        }
    },
    "sort": { "date": { "order": "desc" }}
}
'

多级排序
curl -XGET " http://192.168.56.50:9200/_search" -d '
{
    "query" : {
        "filtered" : {
            "filter" : {
                "term" : {
                    "name" : "john"
                }
            }
        }
    },
    "sort": [
        { "date":   { "order": "desc" }},
        { "_score": { "order": "desc" }}
    ]
}
'


==============================================================
==============================================================
==========================索   引   管   理========================
==============================================================
==============================================================

修改配置文件config/elasticsearch.yml添加配置action.auto_create_index: false来防止自动添加索引

删除索引/删除多个索引/删除匹配索引/删除所有索引
DELETE /my_index
DELETE /index_one,index_two
DELETE /index_*
DELETE /_all


索引配置
参数  解释
number_of_shards    定义一个索引的主分片个数，默认值是 `5`。这个配置在索引创建后不能修改。

number_of_replicas  每个主分片的复制分片个数，默认是 `1`。这个配置可以随时在活跃的索引上修改。


创建一个主分片，没有复制分片的索引
PUT /my_temp_index
{
    "settings": {
        "number_of_shards" :   1,
        "number_of_replicas" : 0
    }
}

获取特定索引配置/获取所有索引配置/获取匹配索引配置
GET http://192.168.56.50:9200/index_001/_settings
GET http://192.168.56.50:9200/_all/_settings
GET http://192.168.56.50:9200/index_*/_settings

动态修改复制索引数量
curl -XPUT "http://192.168.56.50:9200/index_001/_settings" -d '
{
    "number_of_replicas": 1
}
'

standard 分析器是用于全文字段的默认分析器，对于大部分西方语系来说是一个不错的选择

测试分析器分词结果（根据不同的分析器获取不同的结果）
curl -XGET "http://xxx:9200/index_001/_analyze?analyzer=standard" -d '
hello,world! -order by iranw.
'

分析器 是三个顺序执行的组件的结合（字符过滤器，分词器，表征过滤器）

字符过滤器是让字符串在被分词前变得更加“整洁”。 一个分析器可能包含零到多个字符过滤器。

一个分析器 必须 包含一个分词器。分词器将字符串分割成单独的词（terms）或表征（tokens）
standard 分析器使用 standard 分词器将字符串分割成单独的字词，删除大部分标点符号
keyword 分词器输出和它接收到的相同的字符串，不做任何分词处理
whitespace 分词器]只通过空格来分割文本
pattern 分词器]可以通过正则表达式来分割文本

分词结果的 表征流 会根据各自的情况，传递给特定的表征过滤器。
表征过滤器可能修改，添加或删除表征。
stemmer 表征过滤器将单词转化为他们的根形态（root form）
ascii_folding 表征过滤器会删除变音符号
ngram 和edge_ngram 可以让表征更适合特殊匹配情况或自动完成。







