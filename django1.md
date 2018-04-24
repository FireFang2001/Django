### django

##### 安装虚拟环境

```
pip3 install virtualenv
```

##### 安装env环境

```
virtualenv --no-site-packages -p xxxx env
```

xxx为python路径

##### 进入虚拟环境

​    进入cd env/Scripts/文件夹 输入activate

##### 退出虚拟环境

​	deactivate

##### 安装django

```
pip install django==1.11
```

1.11版本兼容2.7到3.6x

##### 创建项目

```
django-admin startproject helloworld
```

###### 创建项目后会生成以下文件：

manage.py： 是Django用于管理本项目的管理集工具，数据库自动生成，数据表的修改等都是通过该文件完成。

**init**.py： 指明该目录结构是一个python包，无初始内容。

seetings.py： Django项目的配置文件，其中定义了本项目的引用组件，项目名，数据库，静态资源，调试模式，域名限制等

urls.py：项目的URL路由映射，实现客户端请求url由哪个模块进行响应。

wsgi.py：定义WSGI接口信息，通常本文件生成后无需改动

##### 启动django项目

```
python manage.py runserver ip:端口
```

可不指定端口，系统会给默认端口8000

##### 创建app

```
python manage.py startapp stu
```

该命令是在工程下创建一个名为stu的app

##### 设置语言

在Settings中设置：

LANGUAGE_CODE = 'zh-hans' 表示中文 LANGUAGE_CODE = 'en-us' 表示英文

设置时区：	TIME_ZONE = 'Asia/Shanghai'

##### 在app中的文件：

init.py:初始化
admin.py: 管理后台注册模型
apps.py: settings.py里面注册app的时候需要使用到。一般不推荐这样使用

```
	from app.apps import AppConfig
	AppConfig.name
```

models.py: 写模型的地方
views.py: 写处理业务逻辑的地方

##### 迁移数据库

```
python manage.py makemigrations
python manage.py migrate
```



