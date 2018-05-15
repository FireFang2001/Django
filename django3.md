#### Django模型对应关系（1对1，1对多，多对多）



#### 1对1： 

OneToOneField

主键和外键是一对一的关系，在关联表中，只能关联一个主表的id拓展表找主表。

##### 创建模型：

```python
class Student(models.Model):
    stu_name = models.CharField(max_length=10)
    stu_sex = models.BooleanField()
    stu_birth = models.DateField()
    stu_create_time = models.DateTimeField(auto_now_add=True)
    stu_operate_time = models.DateTimeField(auto_now=True)
    stu_yuwen = models.DecimalField(max_digits=3, decimal_places=1)
    stu_shuxue = models.DecimalField(max_digits=3, decimal_places=1)
    g_id = models.ForeignKey(Grade, null=True)

    class Meta:
        db_table = 'stu'


class StudentInfo(models.Model):
    stu_addr = models.CharField(max_length=30)
    stu_age = models.IntegerField()
    stu = models.OneToOneField(Student)

    class Meta:
        db_table = 'stu_info'
```

在表中生成的关联字段名称为stu_id

##### 通过学生拓展表去获取学生信息

通过拓展表去获取学生的信息：
学生拓展表的单个对象（stuinfo）.关联字段（stu）

```
stuinfo = StuInfo.objects.all().first()
student = stuinfo.stu
```

##### 通过学生获取关联表中个人信息

通过学生获取关联表的数据：
    学生对象（stu）.关联的表名（StuInfo），即可获取到关联表的数据

```python
 stu = Student.objects.all().first()
 stuInfo = stu.stuInfo
```



#### 1对多：

通过models.Forergnkey()关联外键实现。

##### 创建模型：

```
class Grade(models.Model):
	g_name = models.CharField(max_length=16)
class Student:
    s_name = models.CharField(max_length=10)
    s_age = models.IntegerField(default=1)
    s_grade = models.ForeignKey(Grade, on_delete=PROTECT）
```

通过学生去获取班级信息：

```python
stu = Student.objects.first()
stu.s_grade
```

如下是通过班级获取学生信息（通过一获取多的数据）：

语法：一的对象.多的模型_set

低性能方法：

```
g = Grade.objects.all().first()
s = Student.objects.filter(s_grade=g)
```

高性能方法：

``` 
g = Grate.objects.all().first()
s = g.student_set.all()
```



#### 多对多：

models.ManyToManyField()

##### 创建模型：

```python
class User(models.Model):  # 创建用户模型
	u_name = models.CharField(max_length=32)


class Goods(models.Model):  # 创建商品模型
	g_name = models.CharField(max_length=32)
	g_user = models.ManyToManyField(User)
```

生成表时会有三张表，多出的一张表用来连接两个模型，这长表通过两个外键建立两个一对多来实现多对多。

##### 多对多获取数据

获取第一个用户购买了那些商品

```
u = User.objects.all().first()
allstu = u.goods_set.all()
```

获取指定商品的购买用户信息

```
g = Goods.objects.filter(id=1)[0]
g.g_user.all()
```
