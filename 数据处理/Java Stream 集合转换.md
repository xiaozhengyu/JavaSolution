# ��ȡ��������_��
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

ʵ��1����ȡѧ��ID

```java
List<Student> studentList = studentRepository.findAll();
Set<String> studentIdSet = studentList.stream()
    .map(Student::getStudentId())
    .collect(Collectors.toSet());
```

