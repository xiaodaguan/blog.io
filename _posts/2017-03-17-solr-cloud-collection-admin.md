---
layout: post
title: SolrCloud Collection操作
date: 2017-03-17
categories: blog
tags: [solr, 分布式]
description: 
---

## solr
#### collection
创建Collection
``` shell
http://localhost:8080/solr/admin/collections?action=CREATE&name={Collection名称}&numShards={分片数量}&collection.configName={指定的Collection配置文件名称}
```

#### dih
dataimporthandler全量提交：

``` shell
http://<ip>:<port>/solr/#/<collection>/dataimport//dataimport?command=full-import&clean=true&commit=true&wt=json&indent=true&verbose=false&optimize=false&debug=false

http://172.18.79.36:8080/solr/#/intelligentTyres/dataimport//dataimport?command=full-import&clean=true&commit=true&wt=json&indent=true&verbose=false&optimize=false&debug=false
```

定时发送这个请求即可

## zk

``` shell
Usage: solr zk upconfig|downconfig -d <confdir> -n <configName> [-z zkHost]
         solr zk cp [-r] <src> <dest> [-z zkHost]
         solr zk rm [-r] <path> [-z zkHost]
         solr zk mv <src> <dest> [-z zkHost]
         solr zk ls [-r] <path> [-z zkHost]
         solr zk mkroot <path> [-z zkHost]
```

``` shell
java -classpath "${solrlib}/*" org.apache.solr.cloud.ZkCLI -cmd 
```