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

### 实例1  Map<String,List<UserIdAndDepartId>>   

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

### 实例2 Map<String,Set<UserIdAndDepartId>>

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

### 实例3 Map<String,Set<String>>

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

### 实例4 Map<String,Obj>

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