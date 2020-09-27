# Java Stream 筛选集合元素

 

```java
/**
 * @author xzy
 * @date 2020-04-18 19:38
 * 说明：员工类
 */
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Employee implements Serializable {
    private static final long serialVersionUID = -7427550135122105667L;
    private String id;
    private String name;
    private Integer age;
    private char sex;
    private Double salary;

    public static Set<Employee> getEmployeeSet() {
        Set<Employee> employeeSet = new HashSet<>(5);
        employeeSet.add(new Employee("1", "张三", 21, 'm', 9000.0));
        employeeSet.add(new Employee("2", "李四", 35, 'w', 20000.0));
        employeeSet.add(new Employee("3", "王五", 60, 'm', 7000.0));
        employeeSet.add(new Employee("4", "赵六", 25, '2', 12000.0));
        employeeSet.add(new Employee("5", "钱七", 23, 'm', 15000.0));
        return employeeSet;
    }
}
```



### 1. many to one

```java
public class Main2 {
    public static void main(String[] args) {
        Set<Employee> employeeSet = Employee.getEmployeeSet();
        Employee zhangSan = employeeSet
                .stream()
                .filter(employee -> "张三".equals(employee.getName()))
                .findAny()
                .orElse(null);

        zhangSan = employeeSet
                .stream()
                .filter(employee -> "张三".equals(employee.getName()))
                .findFirst()
                .orElse(null);

        zhangSan = employeeSet
                .stream()
                .filter(employee -> "张三".equals(employee.getName()))
                .findFirst()
                .orElseThrow(() -> new IllegalArgumentException("查无此人"));
    }
}
```