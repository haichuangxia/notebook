- 使用的基本流程
- CRUD的基本构造
- 原理解析

# 基本使用流程

## 实体类定义

| 注解        | 作用                                                         |
| ----------- | ------------------------------------------------------------ |
| @TableName  | 设置表的整体属性，如对应的数据表名，是否自动构建resultMap，如果表名和类名一致则不必管他.如表`user_info`和类`UserInfo`则是自动对应 |
| @TableId    | 设置表的主键等属性                                           |
| @TableField | 字段注解，非主键                                             |



TableFiled注解的属性：使用自动填充的话，创建实体类对象时就不需要手动设置该字段的值了

- FieldFill=INSERT：插入数据时填充字段
- FieldFill=UPDATE:加载数据时填充字段

# CRUD

## 默认的自定义方法

mapper默认的crud方法：

| 需求          | 方法      |
| ------------- | --------- |
| 创建(Create)  | insert()  |
| 读取（read）  | select*() |
| 更新(update)  | update*() |
| 删除(delete） | delete*() |



## xml的编写

mapper.xml：用来存储sql语句，通常与mybatis中的实体类对应。一个mapper的xml文件对应一个Mapper接口。