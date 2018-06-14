# Elasticsearch 入门

全文搜索属于最常见的需求，开源的  [Elasticsearch](https://www.elastic.co/)  是目前全文搜索引擎的首选。

它可以快速地储存、搜索和分析海量数据。维基百科、Stack Overflow、Github 都采用它。

Elasticsearch 的底层是开源库 [Lucene](https://lucene.apache.org/)。但是你没法直接用 Lucene，如果要用 Lucene 必须自己写代码去调用它的接口。Elasticsearch 是 Lucene 的封装，提供了 REST API 的操作接口。

## 一：安装
参考官方安装方法 [Install Elasticsearch with Windows MSI Installer](https://www.elastic.co/guide/en/elasticsearch/reference/current/windows.html)，默认安装即可，路径不能出现非英文字符和空格。

## 二：基本概念
### 2.1 Node 与 Cluster
Elasticsearch 本质上是一个分布式数据库，允许多台服务器协同工作，每台服务器可以运行多个 Elasticsearch 实例。

单个 Elasticsearch 实例称为一个节点（node）。一组节点构成一个集群（cluster）。

### 2.2 Index
Elasticsearch 会索引所有字段，经过处理后写入一个反向索引（Inverted Index）。查找数据的时候，直接查找该索引。

Elasticsearch 数据管理的顶层单位就叫做 Index（索引）。每个 Index 的名字必须是小写。

下面的命令可以查看当前节点的所有 Index。
```
GET _cat/indices?v
```

### 2.3 Document
Index 里面单条的记录称为 Document（文档）。Document 使用 JSON 格式表示，下面是一个例子。
```
{
  "user": "张三",
  "title": "工程师",
  "desc": "数据库管理"
}
```
同一个 Index 里面的 Document，不要求有相同的结构（scheme），但是最好保持相同，这样有利于提高搜索效率。

### 2.4 Type
Document 可以分组，这种分组就叫做 Type，它是虚拟的逻辑分组，用来过滤 Document。

不同的 Type 应该有相似的结构（schema），举例来说，`id`字段不能在这个组是字符串，在另一个组是数值。这是与关系型数据库的表的[一个区别](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html)。性质完全不同的数据（比如`products`和`logs`）应该存成两个 Index，而不是一个 Index 里面的两个 Type（虽然可以做到）。

下面的命令可以列出每个 Index 所包含的 Type。
```
GET _mapping?pretty=true
```
根据[规划](https://www.elastic.co/blog/index-type-parent-child-join-now-future-in-elasticsearch)，Elastic 6.x 版只允许每个 Index 包含一个 Type，7.x 版将会彻底移除 Type。

## 三：基本使用 - Index
新建 Index，可以直接向 Elasticsearch 服务器发出 PUT 请求。下面的例子是新建一个名叫`accounts`的 Index。
```
PUT accounts
```
服务器返回一个 JSON 对象，里面的`acknowledged`字段表示操作成功。
```
{
  "acknowledged": true,
  "shards_acknowledged": true
}
```
然后，我们发出 DELETE 请求，删除这个 Index。
```
DELETE accounts
```
```
{
  "acknowledged": true
}
```

## 四：基本使用 - Document
### 4.1 新增记录
向指定的 /Index/Type 发送 PUT 请求，就可以在 Index 里面新增一条记录。比如，向`/accounts/person`发送请求，就可以新增一条人员记录。
```
POST accounts/person/1
PUT accounts/person/1
{
  "user": "张三",
  "title": "工程师",
  "desc": "数据库管理"
}
```
服务器返回的 JSON 对象，会给出 Index、Type、Id、Version 等信息。
```
{
  "_index": "accounts",
  "_type": "person",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}
```
如果你仔细看，会发现请求路径是`/accounts/person/1`，最后的`1`是该条记录的 Id。它不一定是数字，任意字符串（比如`abc`）都可以。

新增记录的时候，也可以不指定 Id，这时要改成 POST 请求。
```
POST accounts/person
{
  "user": "李四",
  "title": "数据库工程师",
  "desc": "数据库管理 数据库运维监控"
}
```
上面代码中，向`/accounts/person`发出一个 POST 请求，添加一个记录。这时，服务器返回的 JSON 对象里面，`_id`字段就是一个随机字符串。
```
{
  "_index": "accounts",
  "_type": "person",
  "_id": "AWP3G4GQ1RBoeeuQSUtU",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}
```

注意，如果没有先创建 Index（这个例子是`accounts`），直接执行上面的命令，Elasticsearch 也不会报错，而是直接生成指定的 Index。所以，打字的时候要小心，不要写错 Index 的名称。


### 4.2 查看记录
向`/Index/Type/Id`发出 GET 请求，就可以查看这条记录。
```
GET accounts/person/1?pretty=true
```
上面代码请求查看`/accounts/person/1`这条记录，URL 的参数`pretty=true`表示以易读的格式返回。

返回的数据中，`found`字段表示查询成功，`_source`字段返回原始记录。
```
{
  "_index": "accounts",
  "_type": "person",
  "_id": "1",
  "_version": 1,
  "found": true,
  "_source": {
    "user": "张三",
    "title": "工程师",
    "desc": "数据库管理"
  }
}
```

如果 Id 不正确，就查不到数据，`found`字段就是`false`。
```
GET accounts/person/35
```
```
{
  "_index": "accounts",
  "_type": "person",
  "_id": "35",
  "found": false
}
```

### 4.3 删除记录

删除记录就是发出 DELETE 请求。
```
DELETE accounts/person/1
```
```
{
  "found": true,
  "_index": "accounts",
  "_type": "person",
  "_id": "1",
  "_version": 2,
  "result": "deleted",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  }
}
```
```
DELETE accounts/person/1
```
```
{
  "found": false,
  "_index": "accounts",
  "_type": "person",
  "_id": "35",
  "_version": 1,
  "result": "not_found",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  }
}
```
注意：删除不存在的数据也默认为删除成功。

### 4.4 更新记录
更新记录就是使用 PUT 请求，重新发送一次数据。
```
PUT accounts/person/1
{
    "user" : "张三",
    "title" : "工程师",
    "desc" : "数据库管理，软件开发"
}
```
```
{
  "_index": "accounts",
  "_type": "person",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}
```
```
{
  "_index": "accounts",
  "_type": "person",
  "_id": "1",
  "_version": 2,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": false
}
```
可以看到，记录的 Id 没变，但是版本（version）从`1`变成`2`，操作类型（result）从`created`变成`updated`，`created`字段变成`false`，因为这次不是新建记录。

注意：如果数据不存在默认为新增操作，存在为更新操作（POST/PUT）。

## 五、数据查询
### 5.1 返回所有记录
使用 GET 方法，直接请求`/Index/Type/_search`，就会返回所有记录。
```
GET accounts/person/_search
```
```
{
  "took": 1,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 2,
    "max_score": 1,
    "hits": [
      {
        "_index": "accounts",
        "_type": "person",
        "_id": "AWP8LMuk1RBoeeuQSUtd",
        "_score": 1,
        "_source": {
          "user": "李四",
          "title": "数据库工程师",
          "desc": "数据库管理 软件开发"
        }
      },
      {
        "_index": "accounts",
        "_type": "person",
        "_id": "1",
        "_score": 1,
        "_source": {
          "user": "张三",
          "title": "工程师",
          "desc": "数据库管理，软件开发"
        }
      }
    ]
  }
}
```
上面代码中，返回结果的  `took`字段表示该操作的耗时（单位为毫秒），`timed_out`字段表示是否超时，`hits`字段表示命中的记录，里面子字段的含义如下。

> -   `total`：返回记录数，本例是2条。
> -   `max_score`：最高的匹配程度，本例是`1.0`。
> -   `hits`：返回的记录组成的数组。

返回的记录中，每条记录都有一个`_score`字段，表示匹配的程序，默认是按照这个字段降序排列。

### 5.2 全文搜索
Elasticsearch 的查询非常特别，使用自己的[查询语法](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl.html)，要求 GET 请求带有数据体。

```
GET accounts/person/_search
{
  "query" : { "match" : { "desc" : "管理" }}
}
```
上面代码使用 [Match 查询](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl-match-query.html)，指定的匹配条件是`desc`字段里面包含"软件"这个词。返回结果如下。
```
{
  "took": 1,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 2,
    "max_score": 0.5446649,
    "hits": [
      {
        "_index": "accounts",
        "_type": "person",
        "_id": "AWP8LMuk1RBoeeuQSUtd",
        "_score": 0.5446649,
        "_source": {
          "user": "李四",
          "title": "数据库工程师",
          "desc": "数据库管理 软件开发"
        }
      },
      {
        "_index": "accounts",
        "_type": "person",
        "_id": "1",
        "_score": 0.5446649,
        "_source": {
          "user": "张三",
          "title": "工程师",
          "desc": "数据库管理，软件开发"
        }
      }
    ]
  }
}
```

Elasticsearch 默认一次返回10条结果，可以通过`size`字段改变这个设置。

```
GET accounts/person/_search
{
  "query" : { "match" : { "desc" : "管理" }},
  "size" : 1
}
```
```
{
  "took": 3,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 2,
    "max_score": 0.5446649,
    "hits": [
      {
        "_index": "accounts",
        "_type": "person",
        "_id": "AWP8LMuk1RBoeeuQSUtd",
        "_score": 0.5446649,
        "_source": {
          "user": "李四",
          "title": "数据库工程师",
          "desc": "数据库管理 软件开发"
        }
      }
    ]
  }
}
```
上面代码指定，每次只返回一条结果。

还可以通过`from`字段，指定位移。
注意：total就是满足查询条件的总条数，分页获取总条数时不需要额外操作。
```
GET accounts/person/_search
{
  "query" : { "match" : { "desc" : "管理" }},
  "from" : 1,
  "size" : 1
}
```

```
{
  "took": 1,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 2,
    "max_score": 0.5446649,
    "hits": [
      {
        "_index": "accounts",
        "_type": "person",
        "_id": "1",
        "_score": 0.5446649,
        "_source": {
          "user": "张三",
          "title": "工程师",
          "desc": "数据库管理，软件开发"
        }
      }
    ]
  }
}
```
上面代码指定，从位置1开始（默认是从位置0开始），只返回一条结果。

注意：偏移量 = 页码 * 每页条数

### 5.3 逻辑运算
如果有多个搜索关键字， Elasticsearch 认为它们是`or`关系。
```
GET accounts/person/_search
{
  "query" : { "match" : { "desc" : "软件 系统" }}
}
```
上面代码搜索的是`软件 or 系统`。

如果要执行多个关键词的`and`搜索，必须使用[布尔查询](https://www.elastic.co/guide/en/elasticsearch/reference/5.5/query-dsl-bool-query.html)。
```
GET accounts/person/_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "desc": "数据库" } },
        { "match": { "desc": "软件" } }
      ]
    }
  }
}
```

## 六：中文分词

### 6.1 中文分词问题
```
DELETE accounts
```
```
POST accounts/person
{
  "user": "李四",
  "title": "工程师",
  "desc": "李四是中国工程师"
}
```
```
POST accounts/person
{
  "user": "张三",
  "title": "工程师",
  "desc": "张三正在请教李四中"
}
```
```
GET accounts/person/_search
{
  "query" : { "match" : { "desc" : "中国" }}
}
```
```
{
  "took": 1,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 2,
    "max_score": 0.51623213,
    "hits": [
      {
        "_index": "accounts",
        "_type": "person",
        "_id": "AWP8RTb11RBoeeuQSUth",
        "_score": 0.51623213,
        "_source": {
          "user": "李四",
          "title": "工程师",
          "desc": "李四是中国工程师"
        }
      },
      {
        "_index": "accounts",
        "_type": "person",
        "_id": "AWP8RUBd1RBoeeuQSUti",
        "_score": 0.27233246,
        "_source": {
          "user": "张三",
          "title": "工程师",
          "desc": "张三正在请教李四中"
        }
      }
    ]
  }
}
```
搜索到不相干的数据。

### 6.2 中文分词引入
首先，安装中文分词插件。这里使用的是 [ik](https://github.com/medcl/elasticsearch-analysis-ik/)，也可以考虑其他插件（比如 [smartcn](https://www.elastic.co/guide/en/elasticsearch/plugins/current/analysis-smartcn.html)）。

安装很简单，下载 Elasticsearch 5.6.9 相应版本的 [elasticsearch-analysis-ik-5.6.9.zip](https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v5.6.9/elasticsearch-analysis-ik-5.6.9.zip) , 然后解压到 Elasticsearch 安装目录下的 plugins 目录下即可。
接着，重新启动 Elasticsearch ，就会自动加载这个新安装的插件。

然后，新建一个 Index，指定需要分词的字段。这一步根据数据结构而异，基本上，凡是需要搜索的中文字段，都要单独设置一下。

注意：之前存在的 accounts 索引，需要先删除后，再新建：
```
DELETE accounts
```
```
PUT accounts
{
  "mappings": {
    "person": {
      "properties": {
        "user": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        },
        "title": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        },
        "desc": {
          "type": "text",
          "analyzer": "ik_max_word",
          "search_analyzer": "ik_max_word"
        }
      }
    }
  }
}
```
上面代码中，首先新建一个名称为`accounts`的 Index，里面有一个名称为`person`的 Type。`person`有三个字段。

这三个字段都是中文，而且类型都是文本（text），所以需要指定中文分词器，不能使用默认的英文分词器。

Elasticsearch 的分词器称为  [analyzer](https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis.html)。我们对每个字段指定分词器。

```
"user": {
  "type": "text",
  "analyzer": "ik_max_word",
  "search_analyzer": "ik_max_word"
}
```
上面代码中，`analyzer`是字段文本的分词器，`search_analyzer`是搜索词的分词器。`ik_max_word`分词器是插件`ik`提供的，可以对文本进行最大数量的分词。

### 6.3 中文分词现象
```
{
  "took": 1,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 1,
    "max_score": 0.26742277,
    "hits": [
      {
        "_index": "accounts",
        "_type": "person",
        "_id": "AWP8SQm91RBoeeuQSUtj",
        "_score": 0.26742277,
        "_source": {
          "user": "李四",
          "title": "工程师",
          "desc": "李四是中国工程师"
        }
      }
    ]
  }
}
```
问题解决。
