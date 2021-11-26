# Get 请求与日期传递

---

```java
@RestController
@RequestMapping("xxx/")
public class XxxController {
    @GetMapping("/xxx")
    Xxx xxx(@RequestParam Date date) {...}
}
```

问题：Get接收日期数据时一直抛出格式异常

解决办法：

```java
@RestController
@RequestMapping("xxx/")
public class XxxController {
    
    @InitBinder
    protected void init(HttpServletRequest request, ServletRequestDataBinder binder) {
        SimpleDateFormat dateFormat = new SimpleDateFormat(DateUtil.FORMAT_DAY);
        dateFormat.setLenient(false);
        dateFormat.setTimeZone(TimeZone.getTimeZone("GMT+8"));
        binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));
    }
    
    @GetMapping("/xxx")
    Xxx xxx(@RequestParam Date date) {...}
}
```

