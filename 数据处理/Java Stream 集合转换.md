# 抽取对象数据_流
---

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Student{
    private String studentId;
    private String studentName;
}
```

实例1：抽取学生ID

```java
List<Student> studentList = studentRepository.findAll();
Set<String> studentIdSet = studentList.stream()
    .map(Student::getStudentId())
    .collect(Collectors.toSet());
```

