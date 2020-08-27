# 使用 Calendar 计算时间

### 一. 类型转换
#### 1.1 Date → Calendar

```java
public Calendar date2Calendar(Date date){
    Calendar calendar = Calendar.getInstance();
    calendar.setTime(date);
    return calendar;
}
```
#### 1.2 Calendar → Date

```java
public Date calendar2Date(Calendar calendar){
    return calendar.getTime();
}
```

### 二. 时间计算

#### 2.1 ?毫秒

```java
/*
 * 计算两个日期之间的差值：
 *      日期1：2019-12-31 12:30:12
 *      日期2：2020-01-01 13:31:13
 */
Calendar startCalendar = Calendar.getInstance();
Calendar endCalendar = Calendar.getInstance();

// 手动设置时间
startCalendar.set(2019, Calendar.DECEMBER, 31, 12, 30, 12);
endCalendar.set(2020, Calendar.JANUARY, 1, 13, 31, 13);
startCalendar.set(Calendar.MILLISECOND, 0);
endCalendar.set(Calendar.MILLISECOND, 0);

long startCalendarTimeInMills = startCalendar.getTimeInMillis();
long endCalendarTimeInMills = endCalendar.getTimeInMillis();

long differenceInMills = endCalendarTimeInMills - startCalendarTimeInMills;
System.out.println("相差：" + differenceInMills + "（毫秒）");
```

代码执行结果：

> 相差： 90061000（毫秒）
>
> Process finished with exit code 0

#### 2.2 ?秒

```java
/*
 * 计算两个日期之间的差值：
 *      日期1：2019-12-31 12:30:12
 *      日期2：2020-01-01 13:31:13
 */
Calendar startCalendar = Calendar.getInstance();
Calendar endCalendar = Calendar.getInstance();

// 手动设置时间
startCalendar.set(2019, Calendar.DECEMBER, 31, 12, 30, 12);
endCalendar.set(2020, Calendar.JANUARY, 1, 13, 31, 13);
startCalendar.set(Calendar.MILLISECOND, 0);
endCalendar.set(Calendar.MILLISECOND, 0);

long startCalendarTimeInMills = startCalendar.getTimeInMillis();
long endCalendarTimeInMills = endCalendar.getTimeInMillis();
long differenceInMills = endCalendarTimeInMills - startCalendarTimeInMills;

//1秒 = 1000毫秒
long differenceInSecond = differenceInMills / 1000;
System.out.println("相差： " + differenceInSecond + "（秒）");
```

代码执行结果：

> 相差： 90061（秒）
>
> Process finished with exit code 0

#### 2.3 ?分钟

```java
/*
 * 计算两个日期之间的差值：
 *      日期1：2019-12-31 12:30:12
 *      日期2：2020-01-01 13:31:13
 */
Calendar startCalendar = Calendar.getInstance();
Calendar endCalendar = Calendar.getInstance();

// 手动设置时间
startCalendar.set(2019, Calendar.DECEMBER, 31, 12, 30, 12);
endCalendar.set(2020, Calendar.JANUARY, 1, 13, 31, 13);
startCalendar.set(Calendar.MILLISECOND, 0);
endCalendar.set(Calendar.MILLISECOND, 0);

// 计算毫秒差值
long startCalendarTimeInMills = startCalendar.getTimeInMillis();
long endCalendarTimeInMills = endCalendar.getTimeInMillis();
long differenceInMills = endCalendarTimeInMills - startCalendarTimeInMills;

// 1秒 = 1000毫秒
long differenceInSecond = differenceInMills / 1000;

// 1分钟 = 60秒
long differenceInMinute = differenceInSecond / 60;
System.out.println("相差： " + differenceInMinute + "（分钟）");
```

代码执行结果：

> 相差： 1501（分钟）
>
> Process finished with exit code 0

#### 2.4 ?小时

```java
/*
 * 计算两个日期之间的差值：
 *      日期1：2019-12-31 12:30:12
 *      日期2：2020-01-01 13:31:13
 */
Calendar startCalendar = Calendar.getInstance();
Calendar endCalendar = Calendar.getInstance();

// 手动设置时间
startCalendar.set(2019, Calendar.DECEMBER, 31, 12, 30, 12);
endCalendar.set(2020, Calendar.JANUARY, 1, 13, 31, 13);
startCalendar.set(Calendar.MILLISECOND, 0);
endCalendar.set(Calendar.MILLISECOND, 0);

// 计算毫秒差值
long startCalendarTimeInMills = startCalendar.getTimeInMillis();
long endCalendarTimeInMills = endCalendar.getTimeInMillis();
long differenceInMills = endCalendarTimeInMills - startCalendarTimeInMills;

// 1秒 = 1000毫秒
long differenceInSecond = differenceInMills / 1000;

// 1分钟 = 60秒
long differenceInMinute = differenceInSecond / 60;

// 1小时 = 60分钟 = 3600秒
long differenceInHour = differenceInSecond / 3600;
System.out.println("相差： " + differenceInHour + "（小时）");
```

代码执行结果：

> 相差： 25（小时）
>
> Process finished with exit code 0

#### 2.5 ? 时?分?秒?毫秒

```java
/*
 * 计算两个日期之间的差值：
 *      日期1：2019-12-31 12:30:12
 *      日期2：2020-01-01 13:31:13
 */
Calendar startCalendar = Calendar.getInstance();
Calendar endCalendar = Calendar.getInstance();

// 手动设置时间
startCalendar.set(2019, Calendar.DECEMBER, 31, 12, 30, 12);
endCalendar.set(2020, Calendar.JANUARY, 1, 13, 31, 13);
startCalendar.set(Calendar.MILLISECOND, 0);
endCalendar.set(Calendar.MILLISECOND, 0);

// 计算毫秒差值
long startCalendarTimeInMills = startCalendar.getTimeInMillis();
long endCalendarTimeInMills = endCalendar.getTimeInMillis();
long differenceInMills = endCalendarTimeInMills - startCalendarTimeInMills;

// 1秒 = 1000毫秒
long differenceInSecond = differenceInMills / 1000;

// 1分钟 = 60秒
long differenceInMinute = differenceInSecond / 60;

// 1小时 = 60分钟 = 3600秒
long differenceInHour = differenceInSecond / 3600;

System.out.println("相差： " +
        (differenceInSecond / 3600) + "小时" +
        (differenceInSecond % 3600) / 60 + "分钟" +
        (differenceInSecond % 3600 % 60) + "秒" +
        (differenceInMills % 1000) + "毫秒");
```

代码执行结果：

> 相差： 25小时1分钟1秒0毫秒
> 
> Process finished with exit code 0

### 三、时间比较

#### 3.1 两个日期相差几天？

```java
public static void main(String[] args) {

    /*
     * 计算两个日期之间的差值：
     *      日期1：2019-12-31 12:30:12
     *      日期2：2020-01-01 13:31:13
     */
    Calendar calendar1 = Calendar.getInstance();
    Calendar calendar2 = Calendar.getInstance();

    calendar1.set(2019, Calendar.DECEMBER, 31, 12, 30, 12);
    calendar2.set(2020, Calendar.JANUARY, 1, 13, 31, 13);
    calendar1.set(Calendar.MILLISECOND, 0);
    calendar2.set(Calendar.MILLISECOND, 0);

    // 1.获取日期毫秒数
    long timeInMills1 = calendar1.getTimeInMillis();
    long timeInMills2 = calendar2.getTimeInMillis();

    // 2.计算：本日期是自1900年以来的第几天？
    long days1 = timeInMills1 / 1000 / 3600 / 24;
    long days2 = timeInMills2 / 1000 / 3600 / 24;
    System.out.println("日期 " + calendar1.getTime() + 
                       " 是1900年以来的第" + days1 + "天");
    System.out.println("日期 " + calendar2.getTime() + 
                       " 是1900年以来的第" + days2 + "天");

    // 3.计算日期相差的天数
    long differenceDays = days2 - days1;
    System.out.println("两个日期相差：" + differenceDays + "天");
}
```

代码执行结果：

> 日期 Tue Dec 31 12:30:12 CST 2019 是1900年以来的第18261天
> 日期 Wed Jan 01 13:31:13 CST 2020 是1900年以来的第18262天
> 两个日期相差：1天
>
> Process finished with exit code 0

