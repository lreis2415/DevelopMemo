## Mybatis分页插件PageHelper使用

## 配置
```xml
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="druidDataSource"/>
    <property name="configLocation" value="classpath:config/mybatis.xml"/>
    <property name="mapperLocations" value="classpath*:org/egc/cybersolim/xml/*.xml"/>
    <property name="typeAliasesPackage" value="org.egc.cybersolim.domain"/>
    <!-- Mybatis_PageHelper 分页插件配置 -->
    <property name="plugins">
        <array>
            <bean class="com.github.pagehelper.PageHelper">
                <property name="properties">
                    <value>
                        dialect=postgresql
                        offsetAsPageNum=true
                        rowBoundsWithCount=true
                        reasonable=true
                    </value>
                </property>
            </bean>
        </array>
    </property>
</bean>
```

## 使用实例
```java
@RequestMapping("queryByType")
public Map<String, Object> queryByType(@RequestParam("types[]") String[] types, Integer limit, Integer offset, String order)
    {
        String querytype = "";
        for (String type : types) {
            if (Strings.isNullOrEmpty(type))
                continue;
            else
                querytype = type;
        }
        PageHelper.startPage(offset / limit + 1, limit);
        List<Res> list = resService.queryByParentCode(querytype);
        PageInfo pageInfo = new PageInfo(list);//分页信息
        DataTable dt = new DataTable(list, pageInfo.getTotal());
        dt.setPageNumber(pageInfo.getPageNum());
        dt.setPageSize(pageInfo.getPageSize());
        dt.setSortOrder(order);
        Map<String, Object> result = dt.getDataMap();
        return result;
    }
```
