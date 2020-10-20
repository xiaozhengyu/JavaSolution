# 由对象集合创建各种映射

---

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public static class UserIdAndDepartId {
    /**
     * 用户ID
     */
    private String userId;

    /**
     * 部门ID
     */
    private String departId;
}
```

#### 1. List\<Instance>  to  Map<String , List\<Instance>>   

```java
public static void main(String[] args) {
    List<UserIdAndDepartId> userIdAndDepartIdList = new ArrayList<>(3);
    userIdAndDepartIdList.add(new UserIdAndDepartId("0000001", "abc"));
    userIdAndDepartIdList.add(new UserIdAndDepartId("0000002", "abc"));
    userIdAndDepartIdList.add(new UserIdAndDepartId("0000003", "abc"));

    Map<String, List<UserIdAndDepartId>> departId2UserIdSet;
    departId2UserIdSet = userIdAndDepartIdList
            .stream()
            .collect(Collectors.groupingBy( UserIdAndDepartId::getDepartId));
}
```

#### 2. List\<Instance>  to  Map<String , Set\<Instance>>

```java
public static void main(String[] args) {
    List<UserIdAndDepartId> userIdAndDepartIdList = new ArrayList<>(3);
    userIdAndDepartIdList.add(new UserIdAndDepartId("0000001", "abc"));
    userIdAndDepartIdList.add(new UserIdAndDepartId("0000002", "abc"));
    userIdAndDepartIdList.add(new UserIdAndDepartId("0000003", "abc"));

    Map<String, Set<UserIdAndDepartId>> departId2UserIdSet;
    departId2UserIdSet = userIdAndDepartIdList
            .stream()
            .collect(
                    Collectors.groupingBy( //分组
                            UserIdAndDepartId::getDepartId, //分组依据
                            Collectors.toSet() //整理
                    )
            );
}
```

#### 3. List\<Instance>  to  Map<String , Set\<String>>

```java
public static void main(String[] args) {
    List<UserIdAndDepartId> userIdAndDepartIdList = new ArrayList<>(3);
    userIdAndDepartIdList.add(new UserIdAndDepartId("0000001", "abc"));
    userIdAndDepartIdList.add(new UserIdAndDepartId("0000002", "abc"));
    userIdAndDepartIdList.add(new UserIdAndDepartId("0000003", "abc"));

    Map<String, Set<String>> departId2UserIdSet;
    departId2UserIdSet = userIdAndDepartIdList
            .stream()
            .collect(
                    Collectors.groupingBy( //对数据进行分组
                            UserIdAndDepartId::getDepartId, //以部门ID为分组依据
                            Collectors.mapping( //处理分组数据
                                    UserIdAndDepartId::getUserId, //抽取
                                    Collectors.toSet() //整理
                            )
                    )
            );
}
```

#### 4. List\<Instance>  to  Map<String , Instance>

```java
public class Main2 {
    public static void main(String[] args) {
        List<Employee> employeeList = new ArrayList<>(3);
        employeeList.add(new Employee("1001", "张三", 21, 'm', 5000.0));
        employeeList.add(new Employee("1002", "李四", 22, 'm', 5000.0));
        employeeList.add(new Employee("1003", "王五", 23, 'm', 5000.0));

        Map<String, Employee> id2EmployeeMap = employeeList
                .stream()
                .collect(Collectors.toMap(Employee::getId, e -> e));
    }
}
```

#### 5. List<Map<String,String>> to Map<String , Set\<String>>

```java
public static void main(String[] args) {
    List<Map<String, String>> studentCourseInfoList = new ArrayList<>(10);
    Random random = new Random();
    for (int i = 0; i < 10; i++) {
        Map<String, String> studentCourseInfo = new HashMap<>(2);
        studentCourseInfo.put("studentNo", random.nextInt(4) + "");
        studentCourseInfo.put("courseNo", random.nextInt(10) + "");
        studentCourseInfoList.add(studentCourseInfo);
    }

    /*
     * [
     *   {"studentNo":"111","courseNo":"aaa"},
     *   {"studentNo":"111","courseNo":"bbb"},
     *   {"studentNo":"222","courseNo":"ccc"},
     *   {"studentNo":"222","courseNo":"ddd"},......
     * ]
     * convert to:
     * {
     *     "111":["aaa","bbb"],
     *     "222":["ccc","ddd"],......
     * }
     */

    Map<String, Set<String>> student2CourseSetMap = studentCourseInfoList
            .stream()
            .collect(
                    Collectors.groupingBy(
                            // 数据分组
                            sc -> sc.get("studentNo"),
                            // 数据转换
                            Collectors.mapping(
                                    sc -> sc.get("courseNo"),
                                    Collectors.toSet()
                            )
                    )
            );
}
```

#### 6. List<Map<String,String>> to Map<String , String>

```java
public static void main(String[] args) {
    List<Map<String, String>> studentCourseInfoList = new ArrayList<>(10);
    Random random = new Random();
    for (int i = 0; i < 10; i++) {
        Map<String, String> studentCourseInfo = new HashMap<>(2);
        studentCourseInfo.put("studentNo", random.nextInt(4) + "");
        studentCourseInfo.put("courseNo", random.nextInt(10) + "");
        studentCourseInfoList.add(studentCourseInfo);
    }

    /*
     * [
     *   {"studentNo":"111","courseNo":"aaa"},
     *   {"studentNo":"111","courseNo":"bbb"},
     *   {"studentNo":"222","courseNo":"ccc"},
     *   {"studentNo":"222","courseNo":"ddd"},......
     * ]
     * convert to:
     * {
     *     "111":"aaa,bbb",
     *     "222":"ccc,ddd",......
     * }
     */

    Map<String, String> student2CourseSetMap = studentCourseInfoList
            .stream()
            .collect(
                    Collectors.groupingBy(
                            // 数据分组
                            sc -> sc.get("studentNo"),
                            // 数据转换
                            Collectors.mapping(
                                    sc -> sc.get("courseNo"),
                                    Collectors.joining(",")
                            )
                    )
            );
}
```

