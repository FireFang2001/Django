### cookie

cookie用来保存用户信息，在浏览器登录功能上有广泛的应用。cookie保存于浏览器中，当用户通过浏览器访问服务时，服务器生成一个令牌保存在cookie中，在服务器再次收到用户访问请求时，直接验证令牌，通过则直接登录。

设置cookie

```
response.set_cookie(key, value, max_age=None, exprise=None)
```

cookie的参数

- key：键

- value：值

- max_age：过期时间，时间为秒

- expires：过期时间，具体时间，datetime或者timedelta

- path：生效路径

- domain：生效的域名

- secure：HTTPS传输时应设置为true

- httponly：值应用于http传输，JavaScript无法获取

  ```
  max_age设置为0浏览器关闭失效，设置为None永不过期
  exprise=timedelta(days=10) 10天后过期
  ```



实例：

1. 编写一个方法，用于注册用户的账号和密码。
2. 编写一个方法，用于登录用户的账号和密码，并且登录的时候绑定一个加密的参数在cookie上，并且该加密参数也存储在服务端中。 3）在以后的任意一个请求，我们都获取request中的cookies，查看cookie中绑定的参数是否合法，以及查询是否在服务端存储了。 4）如果验证成功则返回请求url的结果信息到页面，如果验证失败则返回错误提醒信息页面
3. 注销登录

######注册帐号方法：

```python
def regist(request):
    if request.method == 'GET':
        return render(request, 'register.html')
    if request.method == 'POST':
        # 注册
        name = request.POST.get('name')
        password = request.POST.get('password')
        password = make_password(password)
        Users.objects.create(u_name=name,
                             u_password=password)
        return HttpResponseRedirect('/u/login/')
```

######登录方法：

```python
def login(request):
    if request.method == 'GET':
        return render(request, 'login.html')
    if request.method == 'POST':
        # 如果登录成功，绑定参数到cooking中，set_cookie
        name = request.POST.get('name')
        password = request.POST.get('password')
        if Users.objects.filter(u_name=name).exists():
            user = Users.objects.get(u_name=name)
            if check_password(password, user.u_password):
                ticket = 'asdfg' # 实际应用令牌一般动态生成，这里简化为固定字符
                # 绑定令牌到cookie
                response = HttpResponse()
                response.set_cookie('ticket', ticket)
                # 存储在服务端
                user.u_ticket = ticket
                user.save()
                return response
            else:
                return HttpResponse('用户密码错误')
        else:
            return HttpResponse('用户不存在')

```

######验证cookie传递的参数是否正确：

```python
def index(request):
    if request.method == 'GET':
        ticket = request.COOKIES.get('ticket')  # 获取cookie中的参数（令牌）
        if not ticket:
            return HttpResponseRedirect('/u/regist/')
        if Users.objects.filter(u_ticket=ticket).exists():  # 在数据库中查询匹配
            stuinfos = StudentInfo.objects.all()
            return render(request, 'index.html', {'stuinfos': stuinfos})  # 匹配成功
        else:
            return HttpResponseRedirect('/u/login/')  # 匹配失败，跳转到登录页面
```

###### 退出时删除cookie：

```python
def logout(request):
    if request.method == "GET":
        response = HttpResponse()
        response.delete_cookie('ticket')
        return response
```

