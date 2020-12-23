# 使用Calendar计算日期需要注意的问题

## 1. getInstance()

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

## 2.set()

相关源码：

```java
/*
 * ...... 
 * <h3>Getting and Setting Calendar Field Values</h3>
 *
 * <p>The calendar field values can be set by calling the <code>set</code>
 * methods. Any field values set in a <code>Calendar</code> will not be
 * interpreted until it needs to calculate its time value (milliseconds from
 * the Epoch) or values of the calendar fields. Calling the
 * <code>get</code>, <code>getTimeInMillis</code>, <code>getTime</code>,
 * <code>add</code> and <code>roll</code> involves such calculation.
 * ......
 */
public abstract class Calendar implements Serializable, Cloneable, Comparable<Calendar> {
    ......
}
```

- 可以通过set方法修改Calendar实例各个Field的值；
- 只有在真正需要使用calendar的值之前，这种改变才会生效；
- get()、getTimeMillis()、getTime()、add()、roll()方法可以触发set方法的修改。

例子：

```java
public static void main(String[] args) {
        Calendar calendar = Calendar.getInstance();
        System.out.println(calendar);

        calendar.set(Calendar.DAY_OF_MONTH, 1);
        System.out.println(calendar);

        calendar.getTime();
        System.out.println(calendar);
}
```

| 输出1                                                        | 输出2                                                        | 输出3                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| java.util.GregorianCalendar[<br/>time=1608696480267,<br/>areFieldsSet=true,<br/>areAllFieldsSet=true,<br/>lenient=true,<br/>firstDayOfWeek=1,<br/>minimalDaysInFirstWeek=1,<br/>ERA=1,<br/>YEAR=2020,<br/>MONTH=11,<br/>WEEK_OF_YEAR=52,<br/>WEEK_OF_MONTH=4,<br/>DAY_OF_MONTH=23,<br/>DAY_OF_YEAR=358,<br/>DAY_OF_WEEK=4,<br/>DAY_OF_WEEK_IN_MONTH=4,<br/>AM_PM=1,<br/>HOUR=0,<br/>HOUR_OF_DAY=12,<br/>MINUTE=8,<br/>SECOND=0,<br/>MILLISECOND=267,<br/>ZONE_OFFSET=28800000,<br/>DST_OFFSET=0] | java.util.GregorianCalendar[<br/>time=?,<br/>areFieldsSet=false,<br/>areAllFieldsSet=true,<br/>lenient=true,<br/>firstDayOfWeek=1,<br/>minimalDaysInFirstWeek=1,<br/>ERA=1,<br/>YEAR=2020,<br/>MONTH=11,<br/>WEEK_OF_YEAR=52,<br/>WEEK_OF_MONTH=4,<br/>DAY_OF_MONTH=1,<br/>DAY_OF_YEAR=358,<br/>DAY_OF_WEEK=4,<br/>DAY_OF_WEEK_IN_MONTH=4,<br/>AM_PM=1,HOUR=0,<br/>HOUR_OF_DAY=12,<br/>MINUTE=8,<br/>SECOND=0,<br/>MILLISECOND=267,<br/>ZONE_OFFSET=28800000,<br/>DST_OFFSET=0] | java.util.GregorianCalendar[<br/>time=1606795680267,<br/>areFieldsSet=true,<br/>areAllFieldsSet=false,<br/>lenient=true,<br/>firstDayOfWeek=1,<br/>minimalDaysInFirstWeek=1,<br/>ERA=1,<br/>YEAR=2020,<br/>MONTH=11,<br/>WEEK_OF_YEAR=49,<br/>WEEK_OF_MONTH=1,<br/>DAY_OF_MONTH=1,<br/>DAY_OF_YEAR=336,<br/>DAY_OF_WEEK=3,<br/>DAY_OF_WEEK_IN_MONTH=1,<br/>AM_PM=1,<br/>HOUR=0,<br/>HOUR_OF_DAY=12,<br/>MINUTE=8,<br/>SECOND=0,<br/>MILLISECOND=267,<br/>ZONE_OFFSET=28800000,<br/>DST_OFFSET=0] |

