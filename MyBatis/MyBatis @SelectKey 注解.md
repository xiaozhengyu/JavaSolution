# Mybatis @SelectKey 注解

原文地址：http://www.mybatis.cn/archives/741.html

[toc]

# [MyBatis @SelectKey注解用法介绍](http://www.mybatis.cn/archives/741.html)

-   时间: 2019-10-13
-   阅读：9433 次
-   分类: [mybatis注解](http://www.mybatis.cn/category/mybatis-annotations/)

备注：本文修订于2020年6月12日

## 1、@SelectKey简介

@SelectKey注解的作用域是方法，效果与<selectKey>标签等同。

@SelectKey注解用在已经被 @Insert 或 @InsertProvider 或 @Update 或 @UpdateProvider 注解了的方法上。若在未被上述四个注解的方法上作 @SelectKey 注解则视为无效。

## 2、@SelectKey的使用注意事项

@SelectKey注解，即听命他人，也指挥别人，主要表现在两个方面：

（1）自身无效的情况。需要前置注解：@Insert 或 @InsertProvider 或 @Update 或 @UpdateProvider，否则无效。
（2）他人无效的情况。如果指定了 @SelectKey 注解，那么 MyBatis 就会忽略掉由 @Options 注解所设置的生成主键。

## 3、@SelectKey的属性

@SelectKey的属性有下面几个：

-   statement属性：填入将会被执行的 SQL 字符串数组。
-   keyProperty属性：填入将会被更新的参数对象的属性的值。
-   before属性：填入 true 或 false 以指明 SQL 语句应被在插入语句的之前还是之后执行。
-   resultType属性：填入 keyProperty 的 Java 类型。
-   statementType属性：填入Statement、 PreparedStatement 和 CallableStatement 中的 STATEMENT、 PREPARED 或 CALLABLE 中任一值填入 。默认值是 PREPARED。

## 4、@SelectKey的应用场景

如果向数据库中插入一条数据，同时有希望返回该条记录的主键，该怎么处理了？有两种情况：

（1）数据库主键不是自增列，需要预先生成
（2）是自增列，插入之后才能获知

这两种情况都可以通过SelectKey解决，第一个种就是before，第二张是after。