## Ibatis/Mybatis模糊查询

### Ibatis中

1.  使用`$`代替`#`。此种方法就是去掉了类型检查，使用字符串连接，不过可能会**有sql注入风险**。

Sql代码 ` select  *  from table1 where name like '%$name$%'  `

2.  使用连接符。不过不同的数据库中方式不同。

    ```sql
    mysql：  select  *  from table1 where name like concat('%', #name#, '%') 
    oracle:  select  *  from table1 where name like '%' || #name# || '%'  
    sql server:  select  *  from table1 where name like '%' + #name# + '%'
    ```

注意：在实际开发中，往往我们需要将模糊查询的空格去掉。为了防止将去除空格放到业务层去，因此建议如下写（oracle 中，其他数据库雷同）：

Sql代码  ` select  *  from table1 where name like '%' || Trim(#name#) || '%'`

****

### **MyBatis中**

```sql
like "%"#{name}"%"   <!--推荐使用--> （psql中有问题）
like '%'||#{name}||'%'
like '%${name}%'
like CONCAT('%',#{name},'%')  
sqlserver: like '%'+#{name}+'%'  
```