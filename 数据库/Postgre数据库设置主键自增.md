# Postgre数据库设置主键自增

---

**方法1：建表时设置**

```sql
reate table test_a
(
    id serial PRIMARY KEY,
    name character
);
```



**方法2：后期修改**

1.   创建序列

     ```sql
     CREATE SEQUENCE test_c_id_seq
         START WITH 1
         INCREMENT BY 1
         NO MINVALUE
         NO MAXVALUE
         CACHE 1;
     ```

     

2.   修改主键

     ```sql
     ALTER TABLE	test_c ALTER COLUMN id SET default nextval('test_c_id_seq'::regclass);
     ```

     如果数据库已经有了数据，想让id从数据库现有数据的id后面继续自增：

     ```sql
     select setval('test_c_id_seq',(select (max(id) from ops_generator_room)))
     ```

     

