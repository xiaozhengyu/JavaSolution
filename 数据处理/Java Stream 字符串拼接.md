# 拼接String集合中的字符串

1. 现有字符串集合 “张三” 、“李四” 、“王五”、“”、null，现需要将集合中的字符串以逗号隔开，拼接成一个字符串。

    ```java
    public class Main2 {
        public static void main(String[] args) {
            Set<String> studentNameSet = new HashSet<>(5);
            studentNameSet.add("张三");
            studentNameSet.add("李四");
            studentNameSet.add("王五");
            studentNameSet.add("");
            studentNameSet.add(null);
    
            String studentNameString = studentNameSet
                    .stream()
                    .filter(name -> name != null && name.length() > 0)
                    .collect(Collectors.joining(","));
    
            System.out.println(studentNameString);
        }
    }
    ```

    输出结果：
    
    ```
    李四,张三,王五
    ```
    
    注意：
    
    - 使用过滤器对无效数据进行过滤；
    - 过滤器可进行连接：filter().filter().filter()…….；

