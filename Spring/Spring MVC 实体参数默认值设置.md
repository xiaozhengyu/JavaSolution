# Spring MVC 实体参数默认值设置

---

## 一、实体参数赋值过程

Controller：

```java
@RestController
@RequestMapping("test/")
public class TestController {
    
    @PostMapping("post_test2")
    public Message postTest(@RequestBody QueryConditionsDTO qc) {
        return MessageBox.ok(qc);
    }
    
}
```

DTO：

```java
@ToString
@Getter
public class QueryConditionsDTO {
    private String strPar;
    private Integer intPar;
    private Date datePar;
    private Integer pageNumber;
    private Integer pageSize;

    public QueryConditionsDTO() {
        System.out.println("QueryConditionsDTO()");
    }

    public QueryConditionsDTO(String strPar, Integer intPar, Date datePar) {
        System.out.println("QueryConditionsDTO(String strPar, Integer intPar, Date datePar)");
        this.strPar = strPar;
        this.intPar = intPar;
        this.datePar = datePar;
    }

    public void setStrPar(String strPar) {
        System.out.println("setStrPar(String strPar)");
        this.strPar = strPar;
    }

    public void setIntPar(Integer intPar) {
        System.out.println("setIntPar(Integer intPar)");
        this.intPar = intPar;
    }

    public void setDatePar(Date datePar) {
        System.out.println("setDatePar(Date datePar)");
        this.datePar = datePar;
    }

    public void setPageNumber(Integer pageNumber) {
        System.out.println("setPageNumber(Integer pageNumber)");
        this.pageNumber = pageNumber;
    }

    public void setPageSize(Integer pageSize) {
        System.out.println("setPageSize(Integer pageSize)");
        this.pageSize = pageSize;
    }
}
```

HttpClient：

```java
POST http://127.0.0.1:8080/test/post_test2
Content-Type: application/json

{
  "strPar": "str",
  "intPar": 0,
  "datePar": 1609743896044,
  "pageNumber": 0,
  "pageSize": 10
}
```

HttpClient 控制台输出：

```
POST http://127.0.0.1:8080/test/post_test2

HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Mon, 04 Jan 2021 07:21:41 GMT
Keep-Alive: timeout=60
Connection: keep-alive

{
  "status": 1,
  "message": "成功",
  "ok": true,
  "data": {
    "strPar": "str",
    "intPar": 0,
    "datePar": "2021-01-04T07:04:56.044+00:00",
    "pageNumber": 0,
    "pageSize": 10
  },
  "fail": false
}

Response code: 200; Time: 226ms; Content length: 156 bytes
```

Spring 控制台输出：

```
QueryConditionsDTO()
setStrPar(String strPar)
setIntPar(Integer intPar)
setDatePar(Date datePar)
setPageNumber(Integer pageNumber)
setPageSize(Integer pageSize)
```

从 Spring 控制台输出的内容可以看出，Spring MVC 实体参数赋值的过程为：

1. NoArgsConstructor
2. Setter

java 中对实例属性进行赋值有4种方式：

1. 声明实例属性时设置初始化
2. 利用实例初始化代码块赋值
3. 构造方法赋值
4. 其他方法赋值

```java
public class QueryConditionsDTO {
    // 1.声明实例属性时设置初始化
    private Integer pageNumber = 0;
    private Integer pageSize = 30;
    
    // 2.利用实例初始化代码块赋值
    {
        this.pageNumber = 0;
        this.pageSize = 30;
    }
    
    // 3.构造方法赋值
    public QueryConditionsDTO() {
        this.pageNumber = 0;this.pageSize = 30;
    }
    public QueryConditionsDTO(Integer pageNumber, Integer pageSize) {
        this.pageNumber = pageNumber;this.pageSize = pageSize;
    }
    
    // 4.其他方法赋值
    public void setPageNumber(Integer pageNumber) {this.pageNumber = pageNumber;}
    public void setPageSize(Integer pageSize) {this.pageSize = pageSize;}
    
}
```

4种方式执行的先后顺序为：1 2 3 4

从上文的测试结果看出，Spring MVC最后一定会调用Setter来设置实体参数的属性，因此使用1 2 3方式设置的初始值会被覆盖。所以，如果需要为实体参数的属性设置默认值，只能在Setter进行。

## 二、实体参数默认值设置

```java
    public void setPageNumber(Integer pageNumber) {       
        this.pageNumber = pageNumber >= 0 ? pageNumber : 0;
    }
    public void setPageSize(Integer pageSize) {
        this.pageSize = pageSize >= 0 ? pageSize : 0;
    }
```

