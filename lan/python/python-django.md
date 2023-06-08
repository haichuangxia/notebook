

# 数据库操作

## 连接数据库

可以定义具体的使用哪个数据库。

### 使用sqllite

默认是使用sqllite，此时需要设置一下管理员账号。

### 使用MySQL

## 数据表的定义和管理

在ORM中，一个Model类主要包括四个部分：

- 继承自django.db.model.Model
- Model元数据生命
- Filed类型字段
- 魔术方法__str__

### 模型类的定义



如果一个类继承了**django.db.models.Model**,则该类表示数据库中的一个表。

``` python
    from django.db import models
    class UserInfo（models.Model）:
          name = models.CharFiled(max_length=100)
          password = models.CharFiled(max_length=100)
```

#### Field字段的数据类型

模型类中需要定义字段的类型。常用的有以下几种数据类型：

| 字段          | 说明                                                         | 字段属性                                                     |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| AutoFiled     | 默然自增主键（Primary_key=Ture），Django 默认建立id字段为主键。 |                                                              |
| CharFiled     | 字符类型                                                     | Max_length=32，字符长度需要明确                              |
| IntgerFiled   | 整型 int                                                     |                                                              |
| DateFiled     | 年月日时间类型                                               | auto_now=True，数据被更新就会更新时间 ；auto_now_add=True，数据第一次参数时产生。 |
| DateTimeFiled | 年月日小时分钟秒时间类型                                     | auto_now=True，数据被更新就会更新时间； auto_now_add=True，数据第一次参数时产生。 |
| DecimalFiled  | 混合精度的小数类型                                           | max_digits=3，限定数字的最大位数(包含小数位)；decimal_places=2，限制小数的最大位数。 |
| BooleanFiled  | 布尔字段，对应数据库 tinyint 类型数据长度只有1位。           | 值为True或False                                              |
| TextFiled     | 用于大文本                                                   |                                                              |



#### Field字段的属性

| 属性         | 默认值 | 作用                                                         |
| ------------ | ------ | ------------------------------------------------------------ |
| blank        | False  | False:字段内容不可为空，True：可选项，主要用于字符型数据类型 |
| unique       | False  | 值是否唯一                                                   |
| null         | False  | True：允许该字段的值为空，主要用于日期等非字符型数据类型     |
| do_index     | False  | 是否创建索引加快查找                                         |
| default      |        | 给一个字段设置默认值                                         |
| primariy_key | False  | 是否设置该字段为主键，默认是以id为主键，如果没有则自动创建id字段 |



#### Meta元数据类

Meta类封装了一些数据库的信息(主要是配置信息)，叫做元数据，但是元数据并不是Model类的字段。在元数据类中，可以定义：

1. abstract： bool类型的变量，如果为True，则不会对应到数据表。

2. ordering：数据的排序方式，由排序方式+字段名指定：，如： `ordering=["-add_time"] #按照降序排序`。默认升序，”-“表示降序。

3. db_data:指定数据表的名称

4. default_permissions：Django 默认会给每一个定义的 Model 设置四个权限即添加、更改、删除，查看，它使用格式：default_permissions=('add','change','delete','view')

5. permissions： 给Model添加额外的权限。格式为：`permissions = [(have_read_permission', '有读的权限')]`

这里列举部分，要进行数据库关系映射设置的时候，再查阅相关文档就i选哪个了。

#### 通过继承Model类定义自己的Model

自定义的Model都必须继承自*django.db.models.Model*。有3种继承的方式：

| 继承方式 | 特点                                                         |
| -------- | ------------------------------------------------------------ |
| 抽象基类 | 最常用，最基础的继承方式，父类是抽象类不会有数据表           |
| 多表继承 | 父类和子类都有数据表                                         |
| 代理模型 | 给父Model添加一些方法或者修改其Meta选项，但是父类Model并不会改变。相当于copy之后再修改。 |





### 数据表关联关系映射

数据表之间的关联方式主要有：

-  一对一
- 一对多：主要是外键关联关系
- 多对多

#### 外键关系

外键约束是表的一个特殊字段，一般与主键约束一同使用，对于两个具有关联关系的表而言，主键所在的表就是主表，外键所在的表就是从表。

> 外键: 两个关系中有相同的属性，在A中其为主键，在B中A的主键即为其外键。外键时表之间字段值的引用关系
>
> SQL：foreign key(idb) references A(idA)
>
> 主表：具有外键的表为主表
>
> 从表：以另一个关系的外键作主键的表
>
> 外键能够保持数据一致性，关联性和完整性，当修改时能够同时修改，
>
> 1. 主表中没有的，从表不允许插入。
> 2. 从表中有的，主表中不允许删除。
> 3. 删除主表中，先删从表。

``` python 
class django.db,model.ForeignKey(to,on_delete,**options)
"""
to: 外键指向谁谁就是从表
on_delete:当主表执行删除操作时对子表的影响
"""
```

主表中定义外键的示例：

``` python 
#一个A类实例对象关联多个B类实例对象
class A(model.Model):
    a
 

class B(model.Model):
    属性 = models.ForeignKey(多对一中"一"的模型类, ...)
        
        
class Article(models.Model):
    # 定义一个外键，指向用户表
    author = models.ForeignKey(
        User,
        null=True,
        on_delete=models.CASCADE,
        related_name='articles'
    )
```




### 使用ORM对数据表进行操作

定义好模型之后，就可以使用模型类继承自**models.Model**的**objects**对象(管理器对象)，**数据的增删改查可以使用objects管理器对象实现**。

objects对象是**django.db.models.Manager**类的一个实例，也被成为**查询管理器**。

#### ORM的CURD操作

``` python
    UserInfo.objects.all()#查询表中的所有记录
    UserInfo.objects.filter(name_contains='j')#查询表中name含有“j”的所有记录,被使用较多
    UserInfo.objects.get(name="john")#有且只有一个查询结果，如果超出一个或者没有,则抛出异常
    UserInfo.objects.get(name="john").delete()#删除名字为john的记录
    UserInfo.objects.get(name="john").update(name='TOM')#更新数据表的name为TOM
```



#### 新建实例

##### 使用objects.create方法创建

``` python
UserInfo.objects.create(name='jay',password='abc123')
```

使用对象管理器的create方法创建

##### 使用save方法创建

``` python
    Obj=UserInfo（name="jay",password="abc123"）
    Obj.name="john"
    Obj.save()
```

对象模板类的save方法创建，需要先创建一个对象，然后再调用对象的save方法进行保存。

#### 查询操作

##### 返回单条查询结果

使用objects对象的get和get_or_create方法可以根据特定条件进行查询。



##### 原生数据库操作方法-raw

通过使用模型管理器(Manager)的raw方法来执行select语句进行数据库u的查询。该方法返回RawQuerySet对象，该对象支持索引和切片，同时也可以对齐迭代得到Model实例对象。



##### 使用p对象和Q对象进行查询



#### 查询多个记录

经过api查询，返回的结果一般都是一个集合，这个集合叫做QuerySet。对queryset操作的api有：

还有其他的一些api，如切片等，需要时再查询。

| api      | 作用                                                |
| -------- | --------------------------------------------------- |
| all      | 获取所有数据记录                                    |
| filter   | 通过过滤返回一个新的queryset，相当于使用了where语句 |
| exclude  | 返回的结果刚好与filter相反                          |
| order_by | 实现自定义排序                                      |



## Django数据库操作API

| API        | 功能                  |
| ---------- | --------------------- |
| len和count | 统计对象数量          |
| exists     | 判断记录是否存在      |
| update     | 更新model实例，用于改 |
| delete     | 用于删除数据记录      |



# 用户登录模块

django提供的**auth**模块能够快速实现用户模块的基本功能。该模块定义了一张名叫**auth_user**的内建用户表，当调用auth模块的相应接口时就会生成该表。

### 内置用户表的格式



<p align=center>auth_user表的结构</p>


| Field        | Type         | Null | Key  | Default | Extra          |
| ------------ | ------------ | ---- | ---- | ------- | -------------- |
| id           | int(11)      | NO   | PRI  | NULL    | auto_increment |
| password     | varchar(128) | NO   |      | NULL    |                |
| last_login   | datetime(6)  | YES  |      | NULL    |                |
| is_superuser | tinyint(1)   | NO   |      | NULL    |                |
| username     | varchar(150) | NO   | UNI  | NULL    |                |
| first_name   | varchar(30)  | NO   |      | NULL    |                |
| last_name    | varchar(150) | NO   |      | NULL    |                |
| email        | varchar(254) | NO   |      | NULL    |                |
| is_staff     | tinyint(1)   | NO   |      | NULL    |                |
| is_active    | tinyint(1)   | NO   |      | NULL    |                |
| date_joined  | datetime(6)  | NO   |      | NULL    |                |

### auth模块的相关功能

| 模块                                  | 功能                                                         | 实例                                                     |
| ------------------------------------- | ------------------------------------------------------------ | -------------------------------------------------------- |
| authenticate                          | 使用关键字传参的方法来传递用户凭证，从而达到用户认证的目的： | user = authenticate(username='root',password='12345abc') |
| django.contrib.auth.models.Permission | 管理用户权限                                                 |                                                          |
| django.contrib.auth.models.Group      | 创建或者删除用户组                                           |                                                          |
|                                       |                                                              |                                                          |



# 后台管理模块

django的后台管理系统在model.admin中定义



## 用户管理

使用python manage.py createsuperuser创建一个超级管理员账户。

## 数据表管理

当新建了一个app后，在models.py中定义了数据表，此时会想在后台管理界面中对该数据表进行可视化管理。此时需要在对应的**admin.py**文件中，将自定义的Model注册到管理后台。注册的代码如下：

``` python
from .models import Article # 导入model类
admin.site.register(Article) # 将Article这个Model注册到管理后台中进行管理
```



# 路由设置

在django中可以通过两种方式进行路由设置：一种是使用urlpatterns的手动路由设置方法，另一个是使用的path()和re_path()方法/

## 使用url方法设置路由

django中通过路由设置将url与对应的处理函数对应起来。Django 中利用 ROOT_URLCONF 构建了 URL 与视图函数的映射关系。在 django.conf.urls 中封装了路由模块。

``` python
    from django.conf.urls import url
    urlpatterns=[
    url(r '^admin/',admin.site.urls),
    ]
```

语法说明：

```python
url(regex,view,name=None)
"""
regex:使用正则表达式来匹配url
view：处理该请求的view函数名称
name：给url地址起别名，用于模板反向解析
"""
```

## 使用path方法设置路由

在主模块中的urlpattern中添加path()函数来添加自模块中的路由。

``` python 
router = DefaultRouter() # 创建router对象
router.register(r'comment', CommentViewSet) # 在路由对象中实现url与视图集之间的对应
urlpatterns = [
    path("admin/", admin.site.urls),
    path('api/', include(router.urls)),
    path('api-auth/', include('rest_framework.urls')),
]
```

使用了path方法之后，不再需要在子模块中添加urls.py文件了，而是需要在router对象中注册对应的url与视图集之间的关系。

path函数的基本用法用途url函数，

``` python
path(route,view,kwargs,name)
"""
route:匹配URL
view：处理请求的视图函数
kwargs：以字典关键字形式给关联的目标视图函数传递参数
name：给url起别名，常用于url的反向解析
"""
```



## 路由反向解析

反向解析其实就是给特定的url起一个别名，或者说定义了个变量，当url的值变的时候，我使用url的地方可以不用改代码。就是一个变量和常量的区别。

#### 命名空间

在include对应的urls文件时，通过namespace防止不同app的url发生冲突。

# reference

1. [django设置数据库](https://docs.djangoproject.com/en/4.2/ref/settings/#std-setting-DATABASE-ENGINE)
2. 