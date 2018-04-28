### Django auth模块

使用Django自带的auth模块可以很便捷的实现用户注册、登录、注销的方法。

首先引入模块

```
from django.contrib.auth.models import User
from django.contrib.auth import authenticate, login, logout
```



##### 注册

```python
def register(request):
    if request.method == 'GET':
        return render(request, 'register.html')
    if request.method == 'POST':
        name = request.POST.get('name')
        password = request.POST.get('password')
        email = request.POST.get('email')
        user = authenticate(username=name, password=password)  # 判断用户是否存在
        if user:
            return render(request, 'register.html')
        User.objects.create_user(name, email, password)  # 创建用户保存在auth_user表中
        return redirect('/u/login/')  # 跳转到登录页面
```

注册的用户数据会保存在auth_user表中，密码会加密保存。


#####登录

```python
def log_in(request):
    if request.method == 'POST':
        name = request.POST.get('name')
        password = request.POST.get('password')
        user = authenticate(username=name, password=password)
        if user:
            login(request, user)
            return HttpResponseRedirect('/u/index/')
    return render(request, 'login.html')
```



##### 注销

```python
def log_out(request):
    logout(request)
    return HttpResponseRedirect('/u/login/')
```

加上装饰器使得方法只能在登录后调用

```python
from django.contrib.auth.decorators import login_required
@login_required(login_url='/u/log/')  # 这设置未登录时跳转路径
def index(request):
    return render(request, 'index.html')
```

