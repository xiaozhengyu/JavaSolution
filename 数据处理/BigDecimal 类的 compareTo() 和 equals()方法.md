# BigDecimal 类的 compareTo() 和 equals()方法

### 1. compareTo()源码

```java
   /**
    * Compares this BigDecimal with the specified BigDecimal. 
    * Two BigDecimal objects that are equal in value but have 
    * a different scale (like 2.0 and 2.00) are considered equal
    * by this method. This method is provided in preference to 
    * individual methods for each of the six boolean comparison 
    * operators（<,<=,==,>,>=,!=）. The suggested idiom for 
    * performing these comparisons is: x.compartTo(y) <op> 0, 
    * where <op> is one of the six comparison operators.
    * 
    * @param  val - BigDecimal to which this BigDecimal is to be 
    *         compared.
    * @return -1,0 or 1 as this BigDecimal is numerically less than,
    *         equal to, or greater than val.
    */
public int compareTo(BigDecimal val) {
    // Quick path for equal scale and non-inflated case.
    if (scale == val.scale) {
        long xs = intCompact;
        long ys = val.intCompact;
        if (xs != INFLATED && ys != INFLATED)
            return xs != ys ? ((xs > ys) ? 1 : -1) : 0;
    }
    int xsign = this.signum();
    int ysign = val.signum();
    if (xsign != ysign)
        return (xsign > ysign) ? 1 : -1;
    if (xsign == 0)
        return 0;
    int cmp = compareMagnitude(val);
    return (xsign > 0) ? cmp : -cmp;
}
```

#### 1.1 注释翻译

将当前 BigDecimal 实例与给定 BigDecimal 实例进行比较。<font color = red>本方法认为，数值相同但精度不同（例如：2.0 和 2.00）的两个 BigDecimal 是相同的。</font> 

本方法的返回值总共有3种：

- -1 ：当前 BigDecimal 的数值小于给定的 BigDecimal。
- 0  ：当前 BigDecimal 的数值等于给定的 BigDecimal。
- 1  ：当前 BigDecimal 的数值大于给定的 BigDecimal。

#### 1.2 实现细节



### 2. equals()源码

```java
/**
 * Compares this BigDecimal with the specified Object for equality. 
 * Unlike compareTo, thi method considers two BigDecimal objects equal
 * only if they are equal in value and scale (thus 2.0 is not equal to 2.00
 * when compared by this method).
 * 
 * @param  x - Object to which this BigDecimal is to to compared.
 * @return   - true if and only if the specified Object is a BigDecimal whose 
 *             value and scale are equal to this BigDecimal`s.
 */
@Override
public boolean equals(Object x) {
    if (!(x instanceof BigDecimal))
        return false;
    BigDecimal xDec = (BigDecimal) x;
    if (x == this)
        return true;
    if (scale != xDec.scale)
        return false;
    long s = this.intCompact;
    long xs = xDec.intCompact;
    if (s != INFLATED) {
        if (xs == INFLATED)
            xs = compactValFor(xDec.intVal);
        return xs == s;
    } else if (xs != INFLATED)
        return xs == compactValFor(this.intVal);

    return this.inflated().equals(xDec.inflated());
}
```

#### 2.1 注释翻译

将当前 BigDecimal 实例与给定 BigDecimal 实例进行比较。<font color = red>本方法认为，只有数值和精度都相同的两个 BigDecimal 才是相同的（因此本方法认为2.0 和 2.00 是不同的）。</font> 

本方法的返回值总共有2种：

- true ：当前 BigDecimal 的数值等于给定的 BigDecimal。
- false：当前 BigDecimal 的数值不等于给定的 BigDecimal。

#### 2.2 实现细节

### 3. 总结

BigDecimal 类的 compartTo 方法和 equals 方法都能用于比较 BigDecimal 实例代表的数值的大小。但两个方法在判别两个数值是否相等的标准上存在一定的差异：compartTo 仅要求两个数的值（value）相等，equals 要求两个数的值和精度（scale）都要相等。

```java
public static void main(String[] args) {
    BigDecimal b1 = new BigDecimal("2.0");
    BigDecimal b2 = new BigDecimal("2.00");

    System.out.println("b1.compareTo(b2):" + (b1.compareTo(b2) == 0));
    System.out.println("b1.equals(b2):" + b1.equals(b2));
}
```

```
b1.compareTo(b2):true
b1.equals(b2):false

Process finished with exit code 0
```

