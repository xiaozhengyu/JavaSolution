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
    private String strPar;
    private Integer intPar;
    private Date datePar;
    private Integer pageNumber;
    private Integer pageSize;

    public QueryConditionsDTO() {
        System.out.println("NoArgumentConstructor");
    }

    public QueryConditionsDTO(String strPar, Integer intPar, Date datePar, Integer pageNumber, Integer pageSize) {
        System.out.println("AllArgumentConstructor");
        this.strPar = strPar;
        this.intPar = intPar;
        this.datePar = datePar;
        this.pageNumber = pageNumber;
        this.pageSize = pageSize;
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
NoArgumentConstructor
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

从上文的测试结果看出，Spring MVC最后一定会调用Setter来设置实体参数的属性，因此使用1 2 3方式设置的初始值会被覆盖。~~所以，如果需要为实体参数的属性设置默认值，只能在Setter进行。~~（该结论后来被证明是错误的，详见下文“追加”部分）

## 二、实体参数默认值设置

```java
    public void setPageNumber(Integer pageNumber) {
        System.out.println("setPageNumber(Integer pageNumber)");
        this.pageNumber = (pageNumber != null && pageNumber >= 0) ? pageNumber : 0;
    }

    public void setPageSize(Integer pageSize) {
        System.out.println("setPageSize(Integer pageSize)");
        this.pageSize = (pageSize != null && pageSize > 0) ? pageSize : 10;
    }
```

## 追加

### 2021年1月8日

**传递所有参数：**

```json
{
  "strPar": "str",
  "intPar": 0,
  "datePar": 1609743896044,
  "pageNumber": null,
  "pageSize": null
}
```

**传递部分参数：**

```json
{
  "strPar": "str",
  "intPar": 0,
  "datePar": 1609743896044
}
```

若前端按照要求的json格式传递所有参数，上文给出的为实体参数设置默认值的方式不会出现问题，但是如果前端只传递了部分参数，这种方式就行不通了：

**传递全部参数HttpClient 控制台输出：**

```
POST http://127.0.0.1:8080/test/post_test2

HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Fri, 08 Jan 2021 01:55:18 GMT
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
```

**传递部分参数HttpClient 控制台输出：**

```
POST http://127.0.0.1:8080/test/post_test2

HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Fri, 08 Jan 2021 01:56:05 GMT
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
    "pageNumber": null,
    "pageSize": null
  },
  "fail": false
}
```

在DTO中打上断点后再次进行测试，对于前端没有传递的那几个参数，系统并没有调用它们对应的Setter，所以上文设置默认值的方式行不通了。这个问题其实也挺好解决，解决方式有2种：

1. 和前端做好约定，确保前端传递全部参数。

2. 上文已经提到，java 中对实例属性进行赋值有四种方式：

    ① 声明实例属性时设置初始化

    ② 利用实例初始化代码块赋值

    ③ 构造方法赋值

    ④ 其他方法赋值

    四种方式执行的先后顺序为：①②③④，Setter属于第4种方式，即最后执行的方式，虽然第4种赋值方式可能不会执行，当前3种方式赋值方式一定会执行，所以只需额外在前3种方式中任选一种进行初始值设置即可：

    ```java
    @Getter
    @ToString
    public class QueryConditionsDTO {
        private String strPar;
        private Integer intPar;
        private Date datePar;
        private Integer pageNumber;
        private Integer pageSize;
    
        public QueryConditionsDTO() {
            System.out.println("NoArgumentConstructor");
            this.pageNumber = 0;
            this.pageSize = 10;
        }
    
        public void setPageNumber(Integer pageNumber) {
            System.out.println("setPageNumber(Integer pageNumber)");
            if(pageNumber != null && pageNumber >= 0){
                this.pageNumber = pageNumber;
            }
        }
    
        public void setPageSize(Integer pageSize) {
            System.out.println("setPageSize(Integer pageSize)");
            if(pageSize != null && pageSize > 0){
                this.pageSize =  pageSize;
            }
        }
        
        // 略去部分Setter
    }
    ```

    处理流程：

    ```mermaid
    graph TB
    start(开始)
    op1[执行无参构造函数,为属性设置默认值]
    if1{调用者是否为属性赋值}
    if2{调用者设置的属性值是否有效}
    op2[按照调用者的要求修改属性值]
    op3[保留系统设置的属性默认值]
    endl(结束)
    
    start-->op1-->if1--Yes-->if2--Yes-->op2-->endl
    if1--No-->op3-->endl
    if2--No-->op3
    ```

    

