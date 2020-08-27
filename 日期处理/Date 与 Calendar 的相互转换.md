# Date 与 Calendar 的相互转换

### 1.Date → Calendar

```java
public Calendar date2Calendar(Date date){
    Calendar calendar = Calendar.getInstance();
    calendar.setTime(date);
    return calendar;
}
```

### 2.Calendar → Date

```java
public Date calendar2Date(Calendar calendar){
    return calendar.getTime();
}
```

