Django

先建立数据库abc

更改settings.py文件

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'abc', #数据库名
        'USER': 'root', # 帐号 
        'PASSWORD': 'root', # 密码
        'PORT': '3306', # 端口
        'HOST': 'localhost' # 地址
    }
}
```

在models.py中创建模型类

```
class Student(models.Model):
    stu_gid = models.IntegerField(null=True, blank=True)
    stu_name = models.CharField(max_length=10, null=True, blank=True)
    stu_sex = models.BooleanField()
    stu_birth = models.DateField()
    stu_tel = models.CharField(max_length=11)

    class Meta:
        db_table = 'stu'
```

##### 迁移数据库

```
python manage.py makemigrations # 生成迁移文件
python manage.py migrate # 迁移数据库
```

##### url() 函数

url() 函数regex、view 、kwargs、name，接下来详细介绍这四个参数。

- regex: 正则表达式，与之匹配的 URL 会执行对应的第二个参数 view。（必选）
- view: 用于执行与正则表达式匹配的 URL 请求。（必选）
- kwargs: 视图使用的字典类型的参数。
- name: 用来反向获取 URL。

url映射路由：

```
from django.conf.urls import url, include
from django.contrib import admin
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^grades/', include('grade.urls')),
]
```

```
from django.conf.urls import url
from grade import views
urlpatterns = [
    url(r'^grade/', views.grade),
]
```



##### 模型查询

######  模型成员objects

Django默认通过模型的objects对象实现模型数据查询

###### 过滤器

查询集表示从数据库获取的对象集合

查询集可以有多个过滤器

过滤器就是一个函数，基于所给的参数限制查询的结果

从SQL角度来说，查询集合和select语句等价，过滤器就像where条件

Django有两种过滤器用于筛选记录 filter	: 返回符合筛选条件的数据集 exclude : 返回不符合筛选条件的数据集

多个filter和exclude可以连接在一起查询

当然还有如下这些过滤器: all() 返回所有数据order_by() 排序 values() 一条数据就是一个字典，返回一个列表

###### 查询单个数据

get()：返回一个满足条件的对象。如果没有返回符合条件的对象，会应该模型类DoesNotExist异常，如果找到多个，会引发模型类MultiObjectsReturned异常

first()：返回查询集中的第一个对象

last()：返回查询集中的最后一个对象

count()：返回当前查询集中的对象个数

exists()：判断查询集中是否有数据，如果有数据返回True，没有返回False

###### 限制查询集

限制查询集，可以使用下表的方法进行限制，等同于sql中的limit

模型名.objects.all()[0:5] 小标不能为负数

###### 字段查询

对sql中的where实现，作为方法，filter(),exclude()，get()的参数

语法：属性名称__比较运算符 = 值

外键：属性名_id

注意：like语句中使用%表示通配符。比如sql语句查询 where name like '%xxx%'，等同于filter(name_contains='xxx')

###### 比较运算符

contains：是否包含，大小写敏感 startswith，endswith：以values开头或者结尾，大小写敏感 以上的运算符前加上i(ignore)就不区分大小写了

isnull，isnotnull：是否为空。filter(name__isnull=True)

in：是否包含在范围内。filter(id__in=[1,2,3])

gt，gte，lt，lte：大于，大于等于，小于，小于等于。filter(age__gt=10)

pk：代表主键，也就是id。filter(pk=1)

###### 聚合函数

ggregate()函数返回聚合函数的值

Avg：平均值

Count：数量

Max：最大

Min：最小

Sum：求和

例如: Student.objects.aggregate(Max('age'))v

###### F对象/Q对象

F对象:可以使用模型的A属性与B属性进行比较 背景:在模型中有两个字段，分别表示学生成绩A与成绩B，要对成绩AB进行比较计算，就需要使用到F对象。 

例如有如下例子，班级中有女生个数字段以及男生个数字段，统计女生数大于男生数的班级可以如下操作:

```
grades = Grade.objects.filter(girlnum__gt=F('boynum'))
```

F对象支持算数运算

```
grades = Grade.objects.filter(girlnum__gt=F('boynum') + 10)
```

Q对象: Q()对象就是为了将过滤条件组合起来

当我们在查询的条件中需要组合条件时(例如两个条件“且”或者“或”)时。我们可以使用Q()查询对象

使用符号&或者|将多个Q()对象组合起来传递给filter()，exclude()，get()等函数

Q()对象的前面使用字符“~”来代表意义“非”

例如查询，学生中不是12岁的或者姓名叫张三的学生，可以如下操作:

```
student = Student.objects.filter(~Q(age=12) | Q(name='张三'))
```

