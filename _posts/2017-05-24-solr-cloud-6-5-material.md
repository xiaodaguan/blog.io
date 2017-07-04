---
layout: post
title: SolrCloud 6.5 相关资料
date: 2017-05-24
categories: blog
tags: [solr, 分布式]
description: 
---
##官方教程：
getting start: https://cwiki.apache.org/confluence/display/solr/Getting+Started+with+SolrCloud
生产模式: https://cwiki.apache.org/confluence/display/solr/Taking+Solr+to+Production

用zk管理配置: https://cwiki.apache.org/confluence/display/solr/Using+ZooKeeper+to+Manage+Configuration+Files

## 其他
SolrCloud生产环境的安装与配置(基于Solr 5.0.0) http://blog.csdn.net/calvin_kui/article/details/44979805


## tips


#### 配置管理

可以通过scrips目录下的zkcli.sh 进行上传配置等操作:
```
cd /opt/solr/server/scripts/cloud-scripts
bash ./zkcli.sh -zkhost localhost:2181 -h
```
也可直接在solr目录下管理配置
```
bin/solr zk -help
```