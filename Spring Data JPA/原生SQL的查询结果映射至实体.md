# 原生SQL的查询结果映射至实体

---

```java
@Service
public class TeacherClassServiceImpl implements TeacherClassService {

    @PersistenceContext
    private EntityManager entityManager;

    /**
     * 分页查询教师对应的班级信息
     *
     * @param pageNumber - 页码
     * @param pageSize   - 页数据条数
     * @return - 教师对应的班级信息
     */
    @Override
    @Transactional(readOnly = true)
    public List<TeacherClassVO> getTeacher2ClassPageable(Integer pageNumber, Integer pageSize) {
        // 创建SQL语句
        String nativeQuerySQL
                = "select " +
                "    t.id as teacherId," +
                "    t.teacher_no as teacherNo," +
                "    t.teacher_name as teacherName," +
                "    c.id as classId," +
                "    c.class_name as className " +
                "from " +
                "    teacher t " +
                "    left join teacher_class tc on tc.teacher_id = t.id " +
                "    left join class c on c.id = tc.class_id ";

        // 利用EntityManager实现查询
        Query query = entityManager.createNativeQuery(nativeQuerySQL);

        // 分页处理
        query.setFirstResult(pageNumber).setMaxResults(pageSize);

        // 结果转换
        query.unwrap(NativeQueryImpl.class).setResultTransformer(Transformers.aliasToBean(TeacherClassVO.class));

        // 获取最终返回的数据
        List<TeacherClassVO> result = query.getResultList();

        return result;
    }
}
```