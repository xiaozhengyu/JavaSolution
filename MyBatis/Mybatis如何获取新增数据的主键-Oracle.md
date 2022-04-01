原文地址：http://www.mybatis.cn/archives/909.html

[toc]

很多时候新增一条数据，仅仅知道操作结果（新增成功）是远远不够的，更多的时候需要这条新增数据的主键，以便下文使用。通常的办法是：先新增，后将其查询出来，这在 MySQL 中有专门的函数可以获取到：SELECT LAST_INSERT_ID()，顾名思义，此函数的目的是获取最后插入的自增 ID。

Mybatis 中通过一些设置可以将 Insert 的数据的主键返回，直接拿到新增数据的主键，以便后续使用，主要有两种场景，分别针对 Mysql 和 Oracle 进行设置。

## 使用 @Options 注解获取主键

我们可以使用 @Options 注解的 userGeneratedKeys 和 keyProperty 属性让数据库产生 auto_increment（自增长）列的值，然后将生成的值设置到输入参数对象的属性中

```java
@Insert("INSERT INTO STUDENTS(NAME,EMAIL,ADRESS,PHONE)  
        VALUES(#{name},#{email},#{address},#{phone})")  
@Options(useGeneratedKeys = true, keyProperty = "id")  
int insertStudent(Student student); 
```

<font color = red>这里 ID 列值将会通过 MySQL 数据库自动生成，并且生成的值将会被设置到 student 对象的 Id 属性中</font>

```java
StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);  
mapper.insertStudent(student);  
int studentId = student.getId();  
```

## 使用 @SelectKey 注解获取主键

有一些数据库如 Oracle，并不支持 AUTO_INCREMENT 列属性，它使用序列（SEQUENCE）来产生主键的值。我们可以使用 @SelectKey 注解来为任意 SQL 语句来指定主键值。

假设我们有一个名为 STUD_ID_SEQ 的序列来生成 ID 主键值：

```java
@Insert("INSERT INTO STUDENTS(ID,NAME,EMAIL,ADDR,PHONE) VALUES(#{id},#{name},#{email},#{address},#{phone})")
@SelectKey(statement="SELECT STUD_ID_SEQ.NEXTVAL FROM DUAL",keyProperty="id", resultType=int.class, before=true)
int insertStudent(Student student); // statement属性执行的SQL语句所返回的结果被赋值给主键
```

这里我们使用了 @SelectKey 来生成主键值，并且存储到了 student 对象的 id 属性上。由于我们设置了 before=true，该语句将会在执行 INSERT 语句之前执行。

如果你使用序列作为触发器来设置主键值，我们可以在INSERT语句执行后，从sequence_name.currval获取数据库产生的主键值。

```java
@Insert("INSERT INTO STUDENTS(NAME,EMAIL,ADDR,PHONE)   
VALUES(#{name},#{email},#{address},#{phone})")  
@SelectKey(statement="SELECT STUD_ID_SEQ.CURRVAL FROM DUAL",   
keyProperty="id", resultType=int.class, before=false)  
int insertStudent(Student student); 
```

### 补充说明：非自增主键的获取方法

设计表的时候有两种主键，一种自增主键，一般为int类型，一种为非自增的主键，例如用uuid等。下面来讲讲非自增主键的获取方法。大致一样，些许不同。

```java
@Insert("INSERT INTO STUDENTS(ID,NAME,EMAIL,ADDR,PHONE)   
VALUES(#{id},#{name},#{email},#{address},#{phone})")  
@SelectKey(statement="SELECT UUID()",   
keyProperty="id", resultType=String.class, before=true)  
int insertStudent(Student student);  
```

跟自增主键方式相比，这里的不同之处只有三点：

-   insert语句需要写id字段了，并且values里面也不能省略。
-   selectKey的order属性需要写成BEFORE，因为这样才能将生成的uuid主键放入到user中，这样后面的insert的values里面的id才不会获取为空。
-   跟自增主键相比就这点区别，这里的获取主键id的方式为select uuid() 。