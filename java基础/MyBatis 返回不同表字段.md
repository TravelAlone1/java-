# MyBatis 返回不同表字段



#### Mapper中SQL

```xml
<select id="getBouhgtBooks" resultType="java.util.Map" parameterType="java.lang.String">
  select book.bkid,bkname,bkfile,bkcover
  from book right join orders on book.bkid=orders.bkid right join user  on user.uid=orders.uid where user.skey=#{skey,jdbcType=VARCHAR}
</select>
```

#### DAO 

```java
//查询用户已购买的书籍
List<Map<String,Object>> getBouhgtBooks(String skey);
```

map本质就是对象，类似以json





```java
@PostMapping("/boughtBooks")
public GlobalResult mybook(String skey){

    List bouhgtBooks = userMapper.getBouhgtBooks(skey);

    return GlobalResult.success(bouhgtBooks);
}
```