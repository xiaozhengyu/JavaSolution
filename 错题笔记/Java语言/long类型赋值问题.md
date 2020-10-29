# long类型赋值问题

---

Java**整数常量**默认为int类型，默认情况下Java整数常量的表数范围为 - 2147483648 ~ 2147483647。

如果想要将一个数值超过int表数范围的整数直接赋值给long，Java 并不会自动把这个整数常量当作long类型来处理。如果希望系统把一个整数常量当作long类型来处理，应该再这个整数常量后增加l或L作为后缀。

```java
public class Main2 {
    public static void main(String[] args) {
        // max_int = 2147483647
        long l1 = 2147483647;  //OK
        long l2 = 2147483648;  //ERROR: Integer number too large
        long l3 = 2147483648L; //OK
    }
}
```

可以将一个数值较小的整数常量（准确的说是数值不超过int类型的表数范围的整数常量）直接赋值给一个long类型的变量，这并不是因为Java会把这个较小的整数常量当成long类型来处理。Java依然把这个整数常量当成int类型来处理，只是这个int类型的值会自动类型转换到long类型。