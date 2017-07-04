---
layout: post
title: mybatis学习笔记
date: 2017-06-06
categories: blog
tags: [mybatis, 持久层]
description: mybatis学习笔记
---

## 简介
是一个持久层框架
支持定制化sql
避免了几乎所有jdbc编码及手动参数设置

## 配置&使用
- 通过maven导入

``` xml
    <dependencies>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.1.1</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.33</version>
        </dependency>
    </dependencies>
```

- 数据库连接配置conf.xml

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <!-- 配置数据库连接信息 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver" />
                <property name="url" value="jdbc:mysql://172.18.79.39:3306/test" />
                <property name="username" value="root" />
                <property name="password" value="cassocialmedia" />
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="mapper.xml"/>
    </mappers>
</configuration>
```

- pojo类，定义属性

``` java
package pojos;
/**
 * Created by guanxiaoda on 2017/6/6.
 */
public class TestData {
    private int id;
    private String title;
    private String content;
    @Override
    public String toString(){
        return "["+id+"] "+title+":"+content;
    }
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getTitle() {
        return title;
    }
    public void setTitle(String title) {
        this.title = title;
    }
    public String getContent() {
        return content;
    }
    public void setContent(String content) {
        this.content = content;
    }
}
```

- mapper接口

``` java
package mapper;
import pojos.TestData;
/**
 * Created by guanxiaoda on 2017/6/6.
 */
public interface TestMapper {
    public TestData getTestData(int id);
}
```

- 定义mapper.xml，对应上面的接口

``` xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD mapper.TestMapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="mapper.TestMapper">
    <select id="getTestData" parameterType="int"
            resultType="pojos.TestData">
        select * from testData where id=#{id}
    </select>
</mapper>
```

- 使用
``` java
public static void main(String[] args) {
        String resource = "conf.xml";
        InputStream is = Thread.currentThread().getContextClassLoader().getResourceAsStream(resource);
        SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(is);
        SqlSession session = ssf.openSession();
        TestMapper mapper = session.getMapper(TestMapper.class);
        TestData data = mapper.getTestData(1);
        System.out.println(data);
        session.close();
    }
```


