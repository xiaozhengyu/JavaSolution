# 使用Calendar计算日期需要注意的问题

### 1. Calendar.getInstance()方法

相关源码：

> Gets a calendar using the default time zone and locale. The Calendar returned is <font color = red>**based on the current time** </font>in the default time zone with the default FORMAT locale.
>
> Returns: a Calendar.

```java
public static Calendar getInstance(){
    return createCalendar(TimeZone.getDefault(),
                          Locale.getDefault(Locale.Category.FORMAT));
}
```

getInstence() 方法基于方法执行瞬间的时间创建 Calendar 实例。所以连续两次调用 getInstance()方法返回的 Calendar 对象代表是时间不相同的。

通过执行以下代码可以明显的查看到这种区别：

```java
public class Main {
    public static void main(String[] args) {
        /*
         * 计算两个日期之间的差值：
         *      日期1：2020-12-31 12:30:15
         *      日期2：2020-12-31 12:20:15
         */
        Calendar calendar1 = Calendar.getInstance();
        Calendar calendar2 = Calendar.getInstance();

        System.out.println("日期1：" + calendar1.getTimeInMillis() + "(毫秒)");
        System.out.println("日期2：" + calendar2.getTimeInMillis() + "(毫秒)");
    }
}
```

我执行上面这段代码时的执行结果：

> 日期1：1598523874732(毫秒)
> 日期2：1598523874755(毫秒)
>
> Process finished with exit code 0