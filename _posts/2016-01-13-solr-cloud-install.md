---
layout: post
title: SolrCloud 4.10 分布式模式搭建
date: 2016-01-13
categories: blog
tags: [solr, 分布式]
description: 
---

# solrcloud 4.10 分布式模式搭建笔记


> 如果是单机模式可能无法使用zookeeper上传solr配置(不确定)
## 准备工作

#### 下载

- zookeeper(3.4.6)
- tomcat(8.0.14)
- solr(4.10.1)

检查java版本 
以上版本大概要求jdk版本在1.7以上，否则有可能出错

#### 安装部署

以下默认在$SOLR_HOME目录进行(将任意位置)

###### 1 部署zookeeper

解压zookeeper到4SOLR_HOME/zookeeper 
编辑zookeeper/conf/zoo.cfg

内容为
```
tickTime=2000 
initLimit=10 
syncLimit=5 
dataDir=$SOLR_HOME/zookeeper/data 
dataLogDir=SOLR_HOME/zookeeper/logs 
clientPort=2181

server.1=172.18.79.34:2888:3888 
server.2=172.18.79.35:2888:3888 
server.3=172.18.79.36:2888:3888
```

将zookeeper复制3份，放到上面所述的3台机器上 
然后在data目录下新建myid文件，内容为数字1(或2，3)

至此配置完成，启动：zookeeper/bin/zkServer.sh start 
然后可以输入zookeeper/bin/zkServer.sh status 查看运行状态，如果3个zookeeper都正常运行，应该有一个leader，另外两个follower

###### 2 部署solr


解压tomcat到$SOLR_HOME/tomcat 
端口用默认不修改，因此可以不用修改tomcat/conf/server.xml文件

解压solr，将其中的example/solr复制并重命名为$SOLR_HOME/solrhome 
将example/webapps/solr.war复制到tomcat/webapps/

创建目录和文件tomcat/conf/Catalina/localhost/solr.xml，内容：

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?> 
<Context docBase="$SOLR_HOME/tomcat/webapps/solr.war" debug="0" crossContext="true" > 
<Environment name="solr/home" type="java.lang.String" value="$SOLR_HOME/solrhome" override="true" /> 
</Context>
```

在solrhome下创建data目录 
编辑solrhome/collection1/conf/solrconfig.xml 
配置此行

```
<dataDir>${solr.data.dir:$SOLR_HOME/solrhome/data}</dataDir>
```

将solr-4.10.1/example/lib/ext下全部jar包放到tomcat/webapps/solr/WEB-INF/lib

将solrhome和tomcat分别复制到3个机器 
启动tomcat 
分别打开http://172.18.79.34-36:8080/solr，显示solr管理页面，则solr配置成功

配置solr cloud
修改tomcat/bin/cataina.sh，在首行上方添加：

JAVA_OPTS="-DzkHost=172.18.79.34:2181,172.18.79.35:2181,172.18.79.36:2181"
重启tomcat

###### 3 用zookeeper管理配置

使用zookeeper可以有两种方式：

用zkCli.sh脚本：`zookeeper/bin/zkCli.sh -cmd help`
使用solr lib包中的zkcli接口：`java -cp "$SOLR_HOME/tomcat/webapps/solr/WEB-INF/lib/*)>" org.apache.solr.cloud.ZkCLI -cmd help `
cp 后面参数为solr的jar包位置，拷贝到任意位置也可 
```
usage: ZkCLI

-c,--collection <arg> for linkconfig: name of the collection 
-cmd cmd to run: bootstrap, upconfig, downconfig, 
linkconfig, makepath, put, putfile,get,getfile, 
list, clear 
-d,--confdir <arg> for upconfig: a directory of configuration files 
-h,--help bring up this help page 
-n,--confname <arg> for upconfig, linkconfig: name of the config set 
-r,--runzk <arg> run zk internally by passing the solr run port - 
only for clusters on one machine (tests, dev) 
-s,--solrhome <arg> for bootstrap, runzk: solrhome location 
-z,--zkhost <arg> ZooKeeper host address 
Examples: 
zkcli.sh -zkhost localhost:9983 -cmd bootstrap -solrhome /opt/solr 
zkcli.sh -zkhost localhost:9983 -cmd upconfig -confdir /opt/solr/collection1/conf -confname myconf 
zkcli.sh -zkhost localhost:9983 -cmd downconfig -confdir /opt/solr/collection1/conf -confname myconf 
zkcli.sh -zkhost localhost:9983 -cmd linkconfig -collection collection1 -confname myconf 
zkcli.sh -zkhost localhost:9983 -cmd makepath /apache/solr 
zkcli.sh -zkhost localhost:9983 -cmd put /solr.conf 'conf data' 
zkcli.sh -zkhost localhost:9983 -cmd putfile /solr.xml /User/myuser/solr/solr.xml 
zkcli.sh -zkhost localhost:9983 -cmd get /solr.xml 
zkcli.sh -zkhost localhost:9983 -cmd getfile /solr.xml solr.xml.file 
zkcli.sh -zkhost localhost:9983 -cmd clear /solr 
zkcli.sh -zkhost localhost:9983 -cmd list
上传默认配置(example/solr/collection1/conf) 
java -cp ".:solrlib-files/*" org.apache.solr.cloud.ZkCLI -cmd upconfig -confdir example/solr/collection1/conf) -confname collection1conf -zkhost 172.18.79.34:2181, 172.18.79.35:2181, 172.18.79.36:2181 
[注：cp后lib文件目录最好用双引号扩起来，否则有可能不能正常运行]
```
链接配置 
```
java -cp ".:solrlib-files/*" org.apache.solr.cloud.ZkCLI -cmd linkconf -collection collection1 -confname collection1conf -zkhost 172.18.79.34:2181, 172.18.79.35:2181, 172.18.79.36:2181
```

修改solrhome/solr.xml中的：

`<int name="hostPort">${jetty.port:8983}</int>`
改为
`<int name="hostPort">${jetty.port:8080}</int>`
重启tomcat

访问solr admin页面，看到cloud菜单，**部署完成**。

