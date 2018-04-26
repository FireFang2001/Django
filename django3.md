#### Django模型对应关系（1对1，1对多，多对多）

关联
1对1： OneToOneField  主键和外键是一对一的关系，在关联表中，只能关联一个主表的id拓展表找主表：



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

```
 stu = Student.objects.all().first()
 stuInfo = stu.stuInfo
注意：通过学生获取关联表的数据的话，语法如下：
    学生对象.关联的表名，即可获取到关联表的数据
```

通过学生获取关联表的数据的话，语法如下：
    学生对象.关联的表名，即可获取到关联表的数据