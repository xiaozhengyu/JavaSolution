# Mybatis 如何获取新增数据的主键？

参考文章：http://www.mybatis.cn/archives/743.html

[toc]



MyBatis 添加记录后获取主键，这是一个很常见的需求。这个需求有分为两种情况：（1）添加单条记录时获取主键值；（2）批量添加记录时获取各记录的主键值。



>   Note：
>
>   1.  MyBatis 从 3.3.1 版本开始支持批量添加记录并返回各记录主键字段值
>
>   2.  本文主要讲述使用 MyBatis 对 MySQL 数据库添加记录后获取主键的方法，不涉及 MS Server 或者 Oracle，敬请注意



## 1、添加单条记录时获取主键

### 方法1：借助 SelectKey 注解或标签

注解：

```java
@SelectKey(statement = "select LAST_INSERT_ID()", resultType = Long.class, keyColumn = "student_id", keyProperty = "studentId", before = false)
@Insert("insert into tbl_student(student_id,student_name) values (#{studentId},#{studentName})")
void saveAndGetId0(StudentEntity studentEntity);
```

标签：

```java
void saveAndGetId1(StudentEntity studentEntity);
```

```xml
<insert id = "saveAndGetId1">
    insert into tbl_student(student_id,student_name) values (#{studentId},#{studentName})
    <selectKey resultType = "long" keyColumn = "student_id" keyProperty = "studentId" order = "AFTER">
        select LAST_INSERT_ID()
    </selectKey>
</insert>
```

### 方法2：借助 Insert 注解或标签

注解：

```java
@Options(useGeneratedKeys = true, keyColumn = "student_id", keyProperty = "studentId")
@Insert("insert into tbl_student(student_id,student_name) values (#{studentId},#{studentName})")
void saveAndGetId2(StudentEntity studentEntity);
```

标签：

```java
void saveAndGetId3(StudentEntity studentEntity);
```

```xml
<insert id = "saveAndGetId3" useGeneratedKeys = "true" keyColumn = "student_id" keyProperty = "studentId">
    insert into tbl_student(student_id,student_name) values (#{studentId},#{studentName})
</insert>
```

## 2、添加多条记录时获取主键

与单条记录插入类似，只不过要设置好 parameterType

```java
void saveAllAndGetIds(List<StudentEntity> studentEntities);
```

```xml
<insert id="saveAllAndGetIds" parameterType="list" useGeneratedKeys = "true" keyColumn = "student_id" keyProperty = "studentId">
    insert into tbl_student(student_id,student_name)
    values
        <foreach collection = "list" item="student" index="index" separator=",">
            (#{student.studentId},#{student.studentName})
        </foreach>
</insert>
```



## 测试

### 通过 Select 注解获取单条新增记录的主键：

```java
@Test
void saveAndGetId0() {
    StudentEntity newRecord = new StudentEntity();
    newRecord.setStudentName(UUID.randomUUID().toString().substring(0, 8));

    log.info("插入前：{}", newRecord.toString());
    studentMapper.saveAndGetId0(newRecord);
    log.info("插入后：{}", newRecord.toString());
}
```

```
2022-04-01 14:09:34.585  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入前：StudentEntity(studentId=null, studentName=3f35b107)
2022-04-01 14:09:34.819 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId0        : ==>  Preparing: insert into tbl_student(student_id,student_name) values (?,?)
2022-04-01 14:09:34.853 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId0        : ==> Parameters: null, 3f35b107(String)
2022-04-01 14:09:34.863 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId0        : <==    Updates: 1
2022-04-01 14:09:34.865 DEBUG 15980 --- [           main] c.x.r.S.saveAndGetId0!selectKey          : ==>  Preparing: select LAST_INSERT_ID()
2022-04-01 14:09:34.865 DEBUG 15980 --- [           main] c.x.r.S.saveAndGetId0!selectKey          : ==> Parameters: 
2022-04-01 14:09:34.893 DEBUG 15980 --- [           main] c.x.r.S.saveAndGetId0!selectKey          : <==      Total: 1
2022-04-01 14:09:34.900  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入后：StudentEntity(studentId=19, studentName=3f35b107)
```

PS：<u>从控制台输出的信息可以看出，底层其实是在新增之后马上执行一条 Select 语句</u>



### 通过 Select 标签获取单条新增记录的主键：

```java
@Test
void saveAndGetId1() {
    StudentEntity newRecord = new StudentEntity();
    newRecord.setStudentName(UUID.randomUUID().toString().substring(0, 8));

    log.info("插入前：{}", newRecord.toString());
    studentMapper.saveAndGetId1(newRecord);
    log.info("插入后：{}", newRecord.toString());
}
```

```
2022-04-01 14:09:34.914  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入前：StudentEntity(studentId=null, studentName=7fce185e)
2022-04-01 14:09:34.914 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId1        : ==>  Preparing: insert into tbl_student(student_id,student_name) values (?,?)
2022-04-01 14:09:34.914 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId1        : ==> Parameters: null, 7fce185e(String)
2022-04-01 14:09:34.925 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId1        : <==    Updates: 1
2022-04-01 14:09:34.925 DEBUG 15980 --- [           main] c.x.r.S.saveAndGetId1!selectKey          : ==>  Preparing: select LAST_INSERT_ID()
2022-04-01 14:09:34.925 DEBUG 15980 --- [           main] c.x.r.S.saveAndGetId1!selectKey          : ==> Parameters: 
2022-04-01 14:09:34.926 DEBUG 15980 --- [           main] c.x.r.S.saveAndGetId1!selectKey          : <==      Total: 1
2022-04-01 14:09:34.927  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入后：StudentEntity(studentId=20, studentName=7fce185e)
```



### 通过 Insert 注解获取单条新增记录的主键：

```java
@Test
void saveAndGetId2() {
    StudentEntity newRecord = new StudentEntity();
    newRecord.setStudentName(UUID.randomUUID().toString().substring(0, 8));

    log.info("插入前：{}", newRecord.toString());
    studentMapper.saveAndGetId2(newRecord);
    log.info("插入后：{}", newRecord.toString());
}
```

```
2022-04-01 14:09:34.932  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入前：StudentEntity(studentId=null, studentName=88a63d8c)
2022-04-01 14:09:34.933 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId2        : ==>  Preparing: insert into tbl_student(student_id,student_name) values (?,?)
2022-04-01 14:09:34.934 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId2        : ==> Parameters: null, 88a63d8c(String)
2022-04-01 14:09:34.943 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId2        : <==    Updates: 1
2022-04-01 14:09:34.946  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入后：StudentEntity(studentId=21, studentName=88a63d8c)
```

PS：从控制台输出的信息可以看出，底层没有额外执行查询语句，所以这种方式的效率应该略高



### 通过 Insert 标签获取单条新增记录的主键：

```java
@Test
void saveAndGetId3() {
    StudentEntity newRecord = new StudentEntity();
    newRecord.setStudentName(UUID.randomUUID().toString().substring(0, 8));

    log.info("插入前：{}", newRecord.toString());
    studentMapper.saveAndGetId3(newRecord);
    log.info("插入后：{}", newRecord.toString());
}
```

```
2022-04-01 14:09:34.953  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入前：StudentEntity(studentId=null, studentName=aa48a0b6)
2022-04-01 14:09:34.954 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId3        : ==>  Preparing: insert into tbl_student(student_id,student_name) values (?,?)
2022-04-01 14:09:34.954 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId3        : ==> Parameters: null, aa48a0b6(String)
2022-04-01 14:09:34.965 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAndGetId3        : <==    Updates: 1
2022-04-01 14:09:34.965  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入后：StudentEntity(studentId=22, studentName=aa48a0b6)
```



### 获取批量新增的所有记录的主键：

```java
@Test
void saveAllAndGetIds() {
    List<StudentEntity> studentEntities = new ArrayList<>(3);
    for (int i = 0; i < 3; i++) {
        StudentEntity newRecord = new StudentEntity();
        newRecord.setStudentName(UUID.randomUUID().toString().substring(0, 8));
        studentEntities.add(newRecord);
        log.info("插入前：{}", newRecord.toString());
    }

    studentMapper.saveAllAndGetIds(studentEntities);
    for (StudentEntity newRecord : studentEntities) {
        log.info("插入后：{}", newRecord.toString());
    }
}
```

```
2022-04-01 14:09:34.972  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入前：StudentEntity(studentId=null, studentName=d547bc65)
2022-04-01 14:09:34.972  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入前：StudentEntity(studentId=null, studentName=f041dde7)
2022-04-01 14:09:34.973  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入前：StudentEntity(studentId=null, studentName=3a1ca34a)
2022-04-01 14:09:35.011 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAllAndGetIds     : ==>  Preparing: insert into tbl_student(student_id,student_name) values (?,?) , (?,?) , (?,?)
2022-04-01 14:09:35.011 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAllAndGetIds     : ==> Parameters: null, d547bc65(String), null, f041dde7(String), null, 3a1ca34a(String)
2022-04-01 14:09:35.023 DEBUG 15980 --- [           main] c.x.r.StudentMapper.saveAllAndGetIds     : <==    Updates: 3
2022-04-01 14:09:35.025  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入后：StudentEntity(studentId=23, studentName=d547bc65)
2022-04-01 14:09:35.025  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入后：StudentEntity(studentId=24, studentName=f041dde7)
2022-04-01 14:09:35.025  INFO 15980 --- [           main] com.xzy.repository.StudentMapperTest     : 插入后：StudentEntity(studentId=25, studentName=3a1ca34a)
```