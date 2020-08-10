## 实体转JSON时，值为null的字段丢失问题

---

1. 有一实体类，其代码如下：

```java
@Data
public class StudentEntity implements Serializable {

    private static final long serialVersionUID = 2127997065197153097L;
    
    private String name;
    private String sex;
    private Integer age;
    private String phone;
}
```

2. 有一Get接口，其代码如下：

```java
@RequestMapping("student/")
@RestController
public class StudentController {

    @GetMapping("get_student")
    public StudentEntity getStudent() {
        StudentEntity student = new StudentEntity();
        student.setName("张三");
        student.setAge(23);
        student.setSex("男");
        // 将‘电话号码’赋值为null
        student.setPhone(null);
        return student;
    }
}
```

3. 调用该接口，接收到的数据如下：

```json
{
  "name": "张三",
  "sex": "男",
  "age": 23,
  "phone":null
}
```

4. 在配置文件添加以下配置:

```properties
spring.jackson.default-property-inclusion=non_null
```

5. 重启项目,再次调用接口,接收到的数据如下:

```json
{
  "name": "张三",
  "sex": "男",
  "age": 23
}
```

可以发现,值为null的’phone’并没有被发送到前端.

6. 移出第4步操作添加的配置,对StudentEntity实体类进行如下修改:

```java
@Data
public class StudentEntity implements Serializable {

    private static final long serialVersionUID = 2127997065197153097L;
    
    private String name;
    private String sex;
    private Integer age;
    
    @JsonInclude(JsonInclude.Include.NON_NULL)
    private String phone;
}
```

7. 重启项目,再次调用接口,接收到的数据如下:

```json
{
  "name": "张三",
  "sex": "男",
  "age": 23
}
```

### 总结

在Spring项目中,后端向前端发送实体数据时,剔除值为null的字段的方式可以有两种:

1. 修改配置文件,添加如下配置:

```properties
spring.jackson.default-property-inclusion=non_null
```

2. 修改实体类,在需要的字段上添加如下注解:

```java
@JsonInclude(JsonInclude.Include.NON_NULL)
```

同理,当前端从后端接收到的数据中部分键值对"不翼而飞",后端可以检查是否系统是否添加了如上配置或注解.