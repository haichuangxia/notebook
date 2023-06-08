

mybatis的三个组件：

- 实体类
- mapper接口
- mapper.xml



选择包装类而不是基本数据类型的原因：

- 如果sql语句返回的是NULL，基本数据类型会抛异常。

xml中sql语句的属性

- id：使用该sql语句的函数名，注意：**不要有多余的空格** 

- parameterType:如果只有一个参数，则该属性指明参数的类型

- resultType：返回的结果类型

  > 返回的结果集和返回类型之间的映射关系:看返回类的属性与结果集的属性(key)的名称是否相等来映射,如果名相同则映射.
  >
  > 如果名称不同,则要么在sql语句需要使用**as**定义别名进行直接映射,要么在xml中使用**resultMap**标签进行间接映射.如类Father中定义了子类Son,此时就需要使用resultMap标签进行间接映射.

``` xml

```





xml配置文件的编写：

``` xml
<!-- 头部约束，不用管，直接复制就行 -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- 这个里面写sql语句 -->

<mapper namespace="com.example.demo.mapper.MusicMapper">
    <select id="selectPages" resultType="com.example.demo.model.dto.MusicDTO">
        select id,name,url from t_music LIMIT #{current},#{size}
    </select>

    <select id="selectCount" resultType="Integer">
        select count(*) as count from t_music
    </select>
</mapper>
```

# 逆向工程 

逆向工程，只能执行**一次**。如果删除了表，则需要删除生成的文件，然后重新生成。

mybatis generator，MBG

# 延迟加载

**延迟加载**：通过简化sql语句，减少要访问的数据库和数据表，减少Java程序与数据库的交互次数来提高效率。也叫做**懒加载**。





# 缓存

还是为了减少应用与数据库的交互次数，提升程序运行效率。类似于内存与cpu之间的缓存。

- 一级缓存：SQL session级别，默认开启不能关闭。sqlsession对象中有一个hashmap对象，用于缓存数据。不同SQL session之间的hashmap相互隔离。执行了curd操作之后，缓存需要清空
- 二级缓存：mapper级别，默认关闭，可以开启。使用时，多个sqlsesion使用同一个mapper的sql语句操作数据库，得到的数据存放在二级缓存区，同样使用hashmap存储。较一级缓存，二级缓存，跨SQL session

配置二级缓存：

- pom.xml中添加依赖

- mapper.xml中添加<cache>标签,配置二级缓存

- mybatis的config.xml中开启二级缓存




# XML映射文件

- parameterType可以由系统自动计算，一般情况下无需手动设置
- resultType：期望从这条语句中返回结果的类全限定名或别名。 注意，如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身的类型。 resultType 和 resultMap 之间只能同时使用一个。
- resultMap：对外部 resultMap 的命名引用



## 顶层元素

- typeAlias元素
- 
- 
- 
- 

> ```java
> package com.someapp.model;
> public class User {
>   private int id;
>   private String username;
>   private String hashedPassword;
> }
> ```

> ```xml
> <!-- 为类型起别名 -->
> <typeAlias type="com.someapp.model.User" alias="User"/>
> 
> <!-- 使用select元素进行查找 -->
> <select id="selectUsers" resultType="User">
>   select id, username, hashedPassword
>   from some_table
>   where id = #{id}
> </select>
> ```

## 参数



## 结果映射

进行结果映射时，默认是根据属性名将列映射到JavaBean的属性上，如果列名和属性名不能匹配上，就需要在select语句中使用as设置别名来完成匹配。



# 动态SQL

动态SQL包含的元素有：

- if
- choose (when, otherwise)
- trim (where, set)
- foreach



## if

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <if test="title != null">
    AND title like #{title}
  </if>
  <if test="author != null and author.name != null">
    AND author_name like #{author.name}
  </if>
</select>
```

## choose，when，otherwise

类似Java中的switch-case-default语句。

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

## trim、where、set

where语句：只会在where里面非空的时候才会插入到sql语句中去



## foreach

*foreach* 元素的功能非常强大，它允许你指定一个集合，声明可以在元素体内使用的集合项（item）和索引（index）变量。

```xml
<select id="selectPostIn" resultType="domain.blog.Post">
  SELECT *
  FROM POST P
  <where>
    <foreach item="item" index="index" collection="list"
        open="ID in (" separator="," close=")" nullable="true">
          #{item}
    </foreach>
  </where>
</select>
```
