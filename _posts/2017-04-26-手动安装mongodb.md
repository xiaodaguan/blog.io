---
layout: post
title: 手动安装mongodb
date: 2017-4-26
categories: blog
tags: [mongodb,实战]
description: 
---


## 下载
```
cd /usr/local
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel62-3.2.7.tgz
tar -xvf mongodb-linux-x86_64-rhel62-3.2.7.tgz
mv mongodb-linux-x86_64-rhel62-3.2.7 mongodb
```
## 环境变量
```
vim /etc/profile

末尾添加
export MONGODB_HOME=/usr/local/mongodb
export PATH=$MONGODB_HOME/bin:$PATH

source /etc/profile
```

## 启动
### 创建目录
```
mkdir -p /data/mongodb
mkdir -p /data/mongodb/log
touch /data/mongodb/log/mongodb.log
```

```
vim /etc/mongodb.conf
```
```
dbpath=/data/mongodb
logpath=/data/mongodb/log/mongodb.log
logappend=true
port=27017
fork=true
##auth = true # 先关闭, 创建好用户在启动
```

```
mongod -f /etc/mongodb.conf
```

## 管理
>mongo
>
### 用户权限
```
db.createUser(
    {
        user: "test",
        pwd: "test",
        roles: [ { role: "readWrite", db: "test" } ]
    }
)
```