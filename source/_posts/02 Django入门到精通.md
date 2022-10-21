---
title: 02 Django入门到精通
date: 2021-03-05 03:17:11.0
updated: 2021-11-09 03:54:54.096
url: /?p=8
categories: 
- 后端
tags: 
- Python
- django
---

 settings.py配置文件
     --- urls路由
     --- wsgi.py wsgiref模块 不考虑
  -- manage.py Django入口文件
  -- db.sqlite3 Django自带的小型数据库
  -- app01文件夹
     --- admin.py Django后台管理
     --- migrations文件夹 数据库前已记录
     --- models.py 数据库相关的模型 模型类(orm)
     --- test.py测试文件
     --- views.py 视图函数
```

## Django三板斧

+ render
+ HttpResponse
+ redirect

# 静态文件配置

```python
默认情况下 
我们将HTML文件放在 template文件夹下
将网站所使用的的静态文件默认存放在static文件夹下

静态文件:
    前端写好了的	能够直接调用使用的文件
    前端写好的js文件
    网站写好的css文件
    网站用到的图片文件
    第三方前端框架

Django默认情况下是不会自动创建static文件夹	需要自己创建
在浏览器中输入url能够看到对应的资源
是因为后端提前开设了该资源的接口
如果访问不到资源 说明后端没有开设该资源的接口

STATIC_URL = '/static/' # 类似于访问静态文件的令牌
"""如果你想要访问静态文件,你就必须以static开头"""
# 静态文件配置
STATICFILES_DIRS = [
    os.path.join(BASE_DIR,'static'),
]

# 静态文件动态解析 使用模板语法
{% load static %}
    <link rel="stylesheet" href="{% static 'bootstrap-...css' %}">
    <script src="{% static 'bootstrap-...js' %}"></script>
    
# form表单默认是get请求
https://127.0.0.1:8000/login/?username=zhangsan&password=124
"""
form表单action参数
1. 不写 默认朝当前所在的url提交数据
2. 全写 指名道姓
3. 只写后缀 /login/
"""

# 前期我们使用Django提交post请求 需要在middleware中注释掉一行代码
# 'django.middleware.csrf.CsrfViewMiddleware',
```

# request对象方法

+ request.method

```python
def login(request):
    # 返回一个登陆界面
    # print(request.method) # 返回请求方式 全大写的字符串形式
    if request.method == 'POST':
        return HttpResponse('登陆成功')
    return HttpResponse('get 方式')
```

+ request.POST

  ```python
  request.POST.get('username')
  request.POST.getlist('hobby')
  # get()只能获得列表中的最后一个元素
  ```

+ request.GET

  ```python
  request.POST.get('username')
  request.POST.getlist('hobby')
  ```

# Django链接mysql

```python
# 第一步
# 'ENGINE': 'django.db.backends.sqlite3',
# 'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
'ENGINE':'django.db.backends.mysql',
'NAME':'day59',
'USER':'root',
'PASSWORD':'xxx',
'HOST':'127.0.0.1',
'PORT':3306,
'CHARSET':'utf8',

# 第二步
Django默认使用mysqldb模块链接MySQL
但是该模块兼容性不好 需要改用pymysql链接

需要告诉Django不要使用默认的mysqldb而是使用pymysql
## 在项目名下的init或者任意的应用名下的init文件中书写以下代码
import pymysql
pymysql.install_as_MySQLdb()
```

# Django ORM

```python
orm 对象关系映射
类					 表
对象					记录
对象属性			   记录某个字段对应的值
```

```python
python3 manage.py makemigrations
python3 manage.py migrate
只要修改了models.py中的属性,就必须重新输入以上两条命令
```

```python
from django.db import models

# Create your models here.
class User(models.Model):
    id = models.AutoField(primary_key=True)
    username = models.CharField(max_length=32,verbose_name='用户名')
    password = models.CharField(max_length=256,verbose_name='密码')
    """
    CharField 必须要指定max_length参数,否则直接报错
    verbose_name 是每一个字段都有的 是对这个字段的解释
    """

class Author(models.Model):
    # 由于一张表中必须要有一个主键字段 并且一般情况下都叫做id字段
    # 所以orm当你不定义主键字段的时候 orm会自动创建一个名为id的主键字段
    # 也就意味着 后续我们在创建模型表的时候如果主键没有特殊的叫法,可以不自己创建
    username = models.CharField(max_length=32)
    password = models.CharField(max_length=256)
```

# 字段的增删改查

```python
# 字段的增加
	1. 可以再终端中给出默认值
    	age = models.IntegerField(verbose_name='年龄')
    2. 该字段可以为空
    	info = models.CharField(max_length=128,null=True)
    3. 直接给该字段设置默认值
    	hobby = models.CharField(max_length=128,default='study')
        
# 字段的修改
	直接修改代码然后执行数据库迁移的两条命令

# 字段的删除(不要轻易地操作)
	对应字段注释,然后执行数据库迁移的两条命令
    执行完毕之后字段对应的数据也就没有了
```

# 数据的增删改查

```python
# 查
res = models.User.objects.filter(username=username)
user_obj = res[0]

user_obj = models.User.objects.filter(username=username).first()
"""
返回值可以看成列表套数据对象的格式
它也支持索引取值
filter内可以携带多个参数 参数之间使用and关系
"""

# 增
## 第一种方式:
res = models.User.objects.create(username=username,password=password)    
返回值就是被创建对象的本身
## 第二种方式
user_obj = models.User()
user_obj.username = username
user_obj.password = password
user_obj.save()

```

```python
# 数据的查改删
# 编辑功能
	点击编辑按钮 朝后端发送编辑数据的请求
   		如何告诉后端用户想要编辑的那一条数据?
        将编辑按钮所在哪一行的数据的主键值发送给后端
        利用url带问号携带参数的方式
    后端查询出用户想要编辑的数据对象 展示到前端页面提供给用户查看和编辑
<<<<<<< HEAD
# 删除功能
	删除数据内部并不是真正的删除
    我们会给数据添加一个表示字段,用来表示当前字段是否被删除
    如果数据被删除了仅仅只是将字段修改了一个状态
    username	password    is_delete
    张三			123			0
    李四			123			1
    
```

# Django orm创建表

Django1.x 版本中外键默认都是级联删除更新的

# Django请求生命周期流程图

![image-20210616155614748](C:\Users\evans\Documents\Typora\web-backend-learning\02 Django入门到精通.assets\image-20210616155614748.png)

# 路由层

## 路由匹配

```Python
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'test',views.test),
    url(r'testadd',views.testadd)
]
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'test/',views.test),
    url(r'testadd/',views.testadd)
]
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^test/$',views.test),
    url(r'^testadd/$',views.testadd)
]
# 首页
url(r'^$',views.index)

# Django自动添加 /
APPEND_SLASH = True/False # 默认True
```

## 无名分组

```Python
"""分组就是将某一段正则表达式 用小括号括起来"""
url(r'^test/(\d+)/',views.test),

def test(request,xxx):
    print(xxx)
    return HttpResponse('test')
# 无名分组就是将括号里正则表达式匹配到的内容作为位置参数 传递给对应的视图函数
```

## 有名分组

```Python
"""可以给正则表达式起一个别名"""
url(r'^testadd/(?P<year>\d+)/',views.test),

# ?P<year> 指代的就是需要传入的关键字参数
def testadd(request,year):
    print(year)
    return HttpResponse('testadd')

# 有名分组就是将括号里正则表达式匹配到的内容作为关键字参数 传递给对应的视图函数
```

## 无名和有名是否可以混合使用

```Python
url(r'index/(\d+)/(?P<year>\d+)',views.index) # 这是错误写法,两者不能混用

url(r'index/(\d+)/(\d+)/(\d+)',views.index)
url(r'index/(?P<year>\d+)/?P<month>(\d+)/(?P<day>\d+)',views.index)
```

## 反响解析

```Python
# 通过一些方法得到一个结果 该结果可以直接访问对应的url出发视图函数
# 先给路由与视图起一个别名
url(r'^func/',views.func,name='ooo')
# 反向解析
	# 后端反响解析
    from django.shortcuts import reverse
    def test(request):
        print(reverse('ooo'))
        return render()
    # 前段反响解析
    <a href="{% url 'ooo' %}">xxx</a>
```

## 无名有名分组反向解析

```Python
# 无名分组反向解析
url(r'index/(\d+)',views.index,name='index')

# 前端
{% url 'index' 123 %}
# 后端
reverse('index',args=(123,))
"""
这个数字写代码的时候应该放什么
	数字一般情况下放的是数据的主键值 数据的编辑和删除
	url(r'^edit/(\d+)/',views.edit,name='edit')
	def edit(request,edit_id):
		reverse('edit',args=(edit_id,))
    
    {% for user in user_queryset %}
    <a href="{{ url 'edit' user.id }}">编辑</a>
    {% endfor %}
"""

作业: 利用无名有名 完成数据的增删改查
    
    

# 有名分组的反向解析
url(r'^func/(?P<year>\d+)',views.func,name='func')
# 后端
def func(request):
    # 写法一:
    print(reverse('func',kwargs={'year':123}))
    # 写法二:
    print(reverse('func',args=(123,)))
# 前端
	# 第二种写法:
	{% url 'index' 123 %}
    # 第一种
    {% url 'index' year=123 %}

```

## 路由分发

每一个应用都可以有自己的templates, urls.py, static

正是基于上述的特点 Django能够非常好的做到分组开发(每个人只写自己的app)

作为组长 只需要将组员写的app全部拷贝到一个新的Django项目中,然后在配置文件里注册所有的app,再利用路由分发的特点,将所有的app整合到一起

当一个项目中的url特别多的时候	总路由urls.py代码非常冗余不好维护,这时候也可以利用路由分发来减轻总路由的压力

```Python
# 总路由
from django.conf.urls import url, include
from app01 import urls as app01_urls
from app02 import urls as app02_urls

url(r'^app01/',include(app01_urls)), # 只要url前缀是app01开头的,全部交给app01处理
url(r'^app02/',include(app02_urls))
# 终极写法(推荐使用)
from django.conf.urls import url, include
url(r'^app01/',include('app01.urls'))
url(r'^app01/',include('app02.urls'))


# 子路由
# app01
urlpatterns = [
    url(r'^reg/',views.reg),
]
# app02
urlpatterns = [
    url(r'^reg/',views.reg),
]
```

## 名称空间(了解)

```Python
# 当多个应用出现了相同的别名 我们研究反向解析会不会自动识别应用前缀

正常情况下反向解析是没有办法自动识别前缀的

这时候我们就需要应用名称空间的概念
在主路由中设置:
    url(r'^app01/',include('app01.urls',namespace='app01')),
    url(r'^app02/',include('app02.urls',namespace='app02'))
    
    reverse('app01:reg')
    reverse('app02:reg')
    
    {% url 'app01:reg' %}
    {% url 'app02:reg' %}
    
其实只要保证名字不冲突 就没有必要使用名称空间
一百年情况下 有多个app的时候我们在起别名的时候会加上app的前缀,这样就可以确保多个app直接名字不会冲突

url(r'^reg/',views.reg,name='app01_reg'),
url(r'^reg/',views.reg,name='app02_reg')
```

## 伪静态(了解)

```Python
"""
将一个动态网页 伪装成静态网页
目的: 在于增大本网站的seo查询力度 并且增加搜索引擎收录本网的概率

搜索引擎本质上就是一个巨大的搜索引擎
"""
```

## 虚拟环境(了解)

```Python
"""
在正常的开发中 我们会给每一个项目配备一个该项目独有的解释器环境
"""
```

## Django版本区别

```Python
1. Django 1.x路由层使用的是 url
Django 2.x/3.x中路由层使用的是path
url() 第一个参数支持正则
path() 第一个参数不支持正则 写什么就标准匹配什么

如果习惯使用正则, 也提供了另外一个方法 re_path
from django.urls import path,re_path
re_path(r'^index/',views.index)
当然也可以自己导入url,然后使用url,但是在2.x和3.x版本中不提倡
from django.conf.urls import url
url(r'^index/',views.index)


2. path支持五种转换器
path('index/<int:id>/',views.index)
def index(request,id)
path('index/<str:string>/')
path('index/<slug:other>/')	# 匹配字母,数字,下划线组成的字符串
path('index/<uuid:uuid>/') # 匹配格式化的UUID
path('index/<path:path>/') # 匹配任何非空字符串,包含了路径分隔符(/) 不能使用?


3. path还支持自定义转换器(了解)
class MonthConverter:
    regex='\d{2}'
	def to_python(self,value):
        return int(value)
    def to_url(self,value):
        return value # 匹配的regex是两个数字,饭后的结果也必须是两个数字
# 注册转换器
from register_converter(MonthConverter,'mon')
path('index/<mon:mon>/',views.index)


4. 1.x 模型层里的外键默认都是级联删除的
但是到了2.x和3.x中需要自己手动设置参数
models.ForeignKey(to='Publish')
models.ForeignKey(to='Publish',on_delete=models.CASCADE...)
```

# 视图层

## 三板斧

+ HttpResponse

  返回字符串类型

+ render

  返回HTML页面 并且在返回浏览器之前还可以个给HTML传值

+ redirect

  重定向

## JsonResponse对象

## form表单上传文件及后端如何获取

```Python
"""
form表单上传文件类型的数据
1. method必须指定为post
2. enctype必须指定为fordata
"""

def ab_file(request):
    if request.method == "POST":
        # print(request.POST.get()) # POST无法获取到文件
        # print(request.FILES)
        file_obj = request.FILES.get('file')  # 文件对象
        print(file_obj.name) # 文件名
        with open(file_obj.name,'wb') as f:
            for line in file_obj.chunks():
                # 官方推荐添加上chunks方法 其实不添加也一样,都是一行一行的读取
                f.write(line)

    return render(request, 'form.html')
```

## request对象方法

```Python
request.method()
request.POST()
request.GET()
request.FILES()
request.path # 只能拿到参数
request.path_info
request.get_full_path() # 不仅能够拿到路径还可以拿到?后面的参数
request.body # 原生的浏览器发过来的二进制数据
```

## FBV与CBV

```Python
# FBV
# 视图函数既可以是函数也可以是类
def index(request):
    return HttpResponse('index')
```

```python 
# CBV
from django.views import View


class MyLogin(View):
    def get(self, request):
        return render(request, 'form.html')

    def post(self, request):
        return HttpResponse('post方法')
    
url(r'^login',views.MyLogin.as_view()),

# FBV和CBV各有千秋
CBV特点:
    能够根据请求方式的不同直接匹配到对应的方法执行
```

## CBV源码(重点)

```Python
# 突破口在urls.py
url(r'^login/',views.MyLogin.as_view())
# url(r'^login/',views.view)  FBV一模一样
# CBV与FBV在路由匹配上本质是一样的 都是路由 对应 函数内存地址
"""
函数名/方法名 加括号执行优先级最高
猜测
    as_view()
        要么是被@staicmethod修饰的静态方法
        要么是被@classmethod修饰的类方法  正确
        
    @classonlymethod
    def as_view(cls, **initkwargs):
        pass
"""

    @classonlymethod
    def as_view(cls, **initkwargs):
        """
        cls就是我们自己写的类   MyCBV
        Main entry point for a request-response process.
        """
        def view(request, *args, **kwargs):
            self = cls(**initkwargs)  # cls是我们自己写的类
            # self = MyLogin(**initkwargs)  产生一个我们自己写的类的对象
            return self.dispatch(request, *args, **kwargs)
            """
            以后你们会经常需要看源码 但是在看python源码的时候 一定要时刻提醒自己面向对象属性方法查找顺序
                先从对象自己找
                再去产生对象的类里面找
                之后再去父类找
                ...
            总结:看源码只要看到了self点一个东西 一定要问你自己当前这个self到底是谁
            """
        return view
      
		# CBV的精髓
    def dispatch(self, request, *args, **kwargs):
        # 获取当前请求的小写格式 然后比对当前请求方式是否合法
        # get请求为例
        # post请求
        if request.method.lower() in self.http_method_names:
            handler = getattr(self, request.method.lower(), self.http_method_not_allowed)
            """
            反射:通过字符串来操作对象的属性或者方法
                handler = getattr(自己写的类产生的对象,'get',当找不到get属性或者方法的时候就会用第三个参数)
                handler = 我们自己写的类里面的get方法
            """
        else:
            handler = self.http_method_not_allowed
        return handler(request, *args, **kwargs)
        """
        自动调用get方法
        """

# 要求掌握到不看源码也能够描述出CBV的内部执行流程(******)
```

## 模板的继承

```Python
# 模板的继承 需要提前序号一个想要继承的模板页面
{% extends 'base.html' %}
之后埋坑+填坑
{% block content %}
{% endblock %}

# 一般情况下我们需要在一个模板中至少埋三个坑
block js
block css
block content
```

## 模板的导入

```Python
# 将页面的某一个局部当成模块的形式 那个地方需要就可以直接导入使用即可
{% include 'module.html' %}
```

# 模型层

## 测试脚本

```Python
"""
当你只是想要测试Django中的某一个py文件内容 那么你可以不用书写前后端交互的形式,而是直接写一个测试脚本即可
脚本代码无论是写在应用下的test文件中,还是自己单独开设py文件都可以
"""
# 测试环境的准备
	# 去manage.py中拷贝前四行代码 然后自己再写两行
import os

if __name__ == "__main__":
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "day05.settings")
# Create your tests here.
    import django
    django.setup()
	from app01 import models
    # 所有的脚本文件都要放在配置文件的下面,否则会运行失败

    models.User.objects.all()
# 然后在这个代码块的下面就可以编写自己的测试环境
```

## 查看内部sql语句的方式

```Python
# 方式1 queryset对象才能够使用query属性查看内部sql语句
res = models.User.objects.values('name','age')
print(res.query) # SELECT `app01_user`.`id`, `app01_user`.`name`, `app01_user`.`age`, `app01_user`.`register_time` FROM `app01_user` WHERE `app01_user`.`id` = 2

# 方式2 所有的sql语句都可以查看
需要在配置文件中的任意位置添加

LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console':{
            'level':'DEBUG',
            'class':'logging.StreamHandler',
        },
    },
    'loggers': {
        'django.db.backends': {
            'handlers': ['console'],
            'propagate': True,
            'level':'DEBUG',
        },
    }
}

```

## 表单操作

### 增删改查

```Python
# 增
    # models.User.objects.create(name='zhangsan', age=18, register_time=datetime.now())
    # user = models.User()
    # user.name = 'lisi'
    # user.age = 19
    # user.register_time = datetime.now()
    # user.save()

    # 删
    # models.User.objects.filter(pk=2).delete()
    # user = models.User.objects.filter(pk=1)
    # user.delete()
    """
    pk会自动查找到当前表的主键字段 指代的就是当前表的逐渐字段
    用了pk之后你就不需要指代当前表的主键字段到底叫什么了
    uid
    pid
    sid
    ...
    """

    # 修改
    # models.User.objects.filter(pk=4).update(name='liskdsb')
    # user = models.User.objects.get(pk=4)
    # user.name = 'wangwu'
    # user.save()
    """
    get方法返回的直接就是当前的数据对象
    但是这个方法不推荐使用
    因为一旦数据不存在代码就会直接报错
    而filter当数据不存在的时候会返回一个空的queryset
    """
```

### 必知必会13条

```Python
 """
    1. all()    查询全部结果
    2. filter() 带过滤条件
    3. get()    直接指定结果
    4. first()  查询结果第一条
    5. last()   查询结果最后一条
    6. values() 可以指定获取的数据字段 select name,age from ...  列表套字典
    models.User.objects.values('name','age')
    <QuerySet [{'zhangsan',18},{'wangwu',19}]>
    7. values_list() 可以指定获取的数据字段   列表套元组
    models.User.objects.values('name','age')
    <QuerySet [('zhangsan',18),('wangwu',19)]>
    8. distinct()   去重
    
    9. order_by()   默认升序 添加"-"变成降序
    10. reverse()   反转的前提是已经经过排序
    models.User.objects.order_by().reverse()
    11. count()     统计个数
    models.User.objects.count()
    12. exclude()   排除..在外
    13. exist()     判断..是否存在(几乎用不到) 因为数据本身自带bool属性
    models.User.objects.filter(pk=10).exists()
    """
```

### 双下划线的使用

```Python
# 大于等于 小于等于
age__lge	age__lte
# 大于 小鱼
age__lg		age__lt
# 或者
age__in=[18,19,17]
# 在某个区段
age__range=[18,40]
# 模糊查询
name__contains='n'
# 是否区分大小写
默认是区分大小写的
想要忽略大小写
name__icontains='n'
# 以..开头
name__startwith='n'
# 以..结尾
name__endwith='n'

## 查询注册时间2021 6月
models.User.objects.filter(register_time__month='6')
models.User.objects.filter(register_time__year='2021')
models.User.objects.filter(register_time__day='6')
models.User.objects.filter(register_time__weekday='6')
```

## 多表操作

### 一对多的外键等删改查

```Python
# 一对多外键增删改查
    # 增
    # 1. 直接写实际字段 id
    models.Book.objects.create(title='西游记', price=123.12, publish_id=1)
    # 2. 虚拟字段 对象
    # publish_obj = models.Publish.objects.filter(pk=2)
    # models.Book.objects.create(title='三国演义', price=123.12, publish_id=publish_obj)

    # 删
    # models.Publish.objects.filter(pk=1).delete() # 级联删除

    # 修改
    # models.Book.objects.filter(pk=1).update(publish_id=2)
    # publish_obj = models.Publish.objects.filter(pk=2)
    # models.Book.objects.filter(pk=1).update(publish_id=publish_obj)
```

### 多对多的外键增删改查

```Python
 # 多对多的增删改查  实际就是第三张关系表的增删改查

    # 如何给书籍添加作者呢? add
    # book_obj = models.Book.objects.filter(pk=1).first()
    # print(book_obj.authors)  # 就类似于你已经到了第三张表了
    # book_obj.authors.add(3,4)
    """
    add给第三张表添加数据 括号内既可以传数字也可以传对象
    """

    # 删 remove
    # book_obj.authors.remove(2)
    # book_obj.authors.remove(1,3)

    # 修改 set 这里需要注意的是set里面必须得放一个可迭代对象,所以可以放列表或者元组
    # book_obj.authors.set([1,2])
    # book_obj.authors.set([1])
    
    # 清空 clear 括号内不携带任何参数
    # 清空第三张关系表某个书籍与坐着的绑定关系
    # book_obj.authors.clear()
```

## 正反向概念

```Python
# 正向
# 反向
外键字段在我手上,我查你就是正向
外键字段不在我手上,我查你就是反向

book >> 外键字段在书上(正向) >> publish
publish >> 外键字段不在publish上 >> book

一对一和多对多正反向判断也是如此
正向查询按字段,反向查询按表名小写加_set

```

# 多表查询

## 子查询(基于对象的跨表查询)

```Python

# 基于对象的跨表查询
# 1. 查询书籍主键为1的出版社
book = models.Book.objects.filter(pk=1).first()
publish = book.publish
print(publish)

# 2. 查询书籍主键为1的作者
book = models.Book.objects.filter(pk=1).first()
# authors = book.authors
# print(authors) # app01.Author.None
# print(authors) # 返回一个queryset对象
authors = book.authors.all()
for i in authors:
    print(i.name)

# 3. 查询作者Jason的电话号码
author = models.Author.objects.filter(name='jason').first()
phone = author.author_detail.phone
print(phone)

"""
正向查询什么时候需要添加 .all()
    当你的结果可能有多个的时候需要加 .all()
    如果是查询结果只有一个,那么直接拿到数据对象
    book.publish
    book.authors.all()
    author.author_detail.phone
"""
# 4. 查询出版社是东方出版社的书
publish = models.Publish.objects.filter(name='东方出版社').first()
books = publish.book_set.all()
print(books)
for i in books:
    print(i.title)

# 5. 查询作者是Jason的书
author = models.Author.objects.filter(name='jason').first()
books = author.book_set.all()
for i in books:
    print(i.title)

# 6. 查询手机号是120的作者姓名
author_detail = models.AuthorDetail.objects.filter(phone=120).first()
name = author_detail.author.name
print(name)

"""
什么时候需要使用_set?
    当结果只有一个的时候不需要,结果多个的时候需要加_set
"""

```



## 连表查询(基于双下划线的跨表查询)

```Python
# 基于双下划线的跨表查询
# 1. 查询Jason的手机号
phone = models.Author.objects.filter(name='jason').values('author_detail__phone')
print(phone)

# 2. 查询书籍主键为1的出版社名称和书名还有作者姓名
res = models.Book.objects.filter(pk=1).values('title','authors__name','publish__name')
print(res)


# 3. 查询Jason的手机号和作者姓名
res = models.AuthorDetail.objects.filter(author__name='jason').values('phone','author__name')
print(res)

# 4.查询书籍主键是1的作者的手机号
# book author authordetail
res = models.Book.objects.filter(pk=1).values('authors__author_detail__phone')
print(res)
"""
只要掌握了正反向
以及双下划线
那么你可以无限制的跨表
"""
```

# 聚合查询&分组查询

```Python
# 聚合查询  aggregate
"""
只要是跟数据库相关的模块
    基本都在django.db.models里面
    如果上面没有那么应该在django.db里面
"""
# 需要先导入
from django.db.models import Max, Min, Count, Avg, Sum

# 1. 计算所有书的凭据价格
res = models.Book.objects.aggregate(Avg('price'),Max('price'),Min('price'),Count('title'),Sum('price'))
print(res)

# 分组查询 annotate
"""
MySQL分组查询特点:
    分组之后默认只能获取到分组的依据 组内的其他字段都无法获取
        严格模式    only_full_group_by
"""
# 1. 统计每一个本书的作者个数
res = models.Book.objects.annotate(author_num=Count('authors__pk')).values('title','author_num') # models后面点什么就是根据什么分组

# 2. 统计每一个出版社卖的最便宜的价格
res = models.Publish.objects.annotate(min_price=Min('book__price')).values('name','min_price')
print(res)

# 3. 统计不止一个作者的书
# 先按照图书分组
# 然后过滤出不止一个作者的图书
res = models.Book.objects.annotate(author_num=Count('authors')).filter(author_num__gt=1).values('title','author_num')
"""
只要你的orm语句执行的结果还是一个queryset对象,你就可以无限制的使用queryset对象方法
"""
# 4. 查询每个作者出的书的总价格
res1 = models.Author.objects.annotate(sum_price=Sum('book__price')).values('name', 'sum_price')
print(res)
```

# F与Q查询

```python
# F与Q查询
from django.db.models import F, Q
# F查询
# 1. 查询卖出数大于库存的书籍
# res = models.Book.objects.filter(sell__gt=F('stock'))
# print(res)

# 2. 将所有书籍的价格都提升50块
models.Book.objects.update(price=F('price')+100)

# 3. 将所有书的名字后面添加上 "爆款" 两个字
"""在操作字符类型的数据的时候 F不能够直接做到字符串的拼接"""
# from django.db.models.functions import Concat
# from django.db.models import Value
# models.Book.objects.update(title=Concat(F('title'),Value(' 爆款')))
# models.Book.objects.update(title=F('title')+'爆款') # 所有的名称会直接变成空白

# Q查询
# 1. 查询卖出数大于100或者价格小于600的书
# res = models.Book.objects.filter(stock__gt=100,price__lt=600)
"""filter括号内的多个参数是and关系"""
# print(res)  # <QuerySet []>
# res = models.Book.objects.filter(Q(stock__gt=100)|Q(price__lt=600))
# print(res)

# Q的高阶用法 能够将查询条件的左边也变成字符串的形式
q = Q()
q.connector = 'or'
q.children.append(('sell__gt',100))
q.children.append(('stock__lt',600))
res = models.Book.objects.filter(q) # filter内也支持直接放q对象,默认还是and关系
print(res)
```

# Django如何开启事务

```Python
"""
原子性
	不可分割的最下单位
一致性
	与原子性相辅相成
隔离性
	事务之间相互隔离
持久性
	事务一旦确认永久生效
	
事务的回滚 rollback
事务的确认 commit
"""

# django中开启事务

from django.db import transaction
try:
    with transaction.atomic():
        # sql1
        # sql2
        # 在with代码块内书写的所有orm操作都是属于同一个事务
except Exception as e:
    print(e)
print('执行其他操作')
```

# orm常用字段和参数

```Python
1. AutoField
	主键字段 primary_key=True
2. CharField
	max_length
    verbose_name
3. IntegerField
4. BigIntegerField
5. DecimalField
	max_digits
    decimal_places
6. EmailField
7. DateTimeField
8. DateField
	auto_now
    auto_now_add
9. BooleanField(Field)  布尔值类型
	该字段传布尔值(False/True) 数据库里对应0/1
10. TextField(Field)  文本类型 
	该字段可以用来存放大段内容(文章,博客...) 没有字数限制
11. FileField(Field)	文件字段
	upload_to = "/data"	给该字段传一个文件对象,会自动保存到/date目录下,然后将文件路径存储到数据库

# django除了给你提供了很多的字段类型之外 还支持自定义类型字段
```

# 数据库查询优化

+ ## only与defer

```Python
# orm语句的特点
惰性查询
如果仅仅只是书写了orm语句,但是在后面并没有用到该语句查询出来的参数,那么不会进行数据库查询

# 想要获取书籍表中的所有书籍的名字
res = models.Books.objects.values('title')
for title in res:
    print(title.get('title'))

res = models.Book.objects.only('title')
for i in res:
    print(i.title) # 点击only括号中的字段 不会走数据库
    print(i.price) # 点击only括号内的没有的字段 会重新走数据库查询 而all不需要走了

res = models.Book.objects.defer('title')
for i in res:
    print(i.price)
"""
defer与only刚好相反
defer括号内的字段不在查询出来的对象里面 查询该字段重新走数据库
而如果查询的是非括号内的字段 则不需要走数据库了
"""
```

+ ## select_related 与 prefetch_related

```Python
# select_related与prefetch_related  跟跨表操作有关
# res = models.Book.objects.all()
# for i in res:
#     print(i.publish.name)  # 每循环一次就要走一次数据库查询

# res = models.Book.objects.select_related('authors')  # INNER JOIN
"""
select_related内部直接先将book与publish连起来 然后一次性将大表里面的所有数据
全部封装给查询出来的对象
    这个时候对象无论是点击book表的数据还是publish的数据都无需再走数据库查询了

select_related括号内只能放外键字段    一对多 一对一
    多对多也不行

"""
# for i in res:
#     print(i.publish.name)  # 每循环一次就要走一次数据库查询

res = models.Book.objects.prefetch_related('publish')  # 子查询
"""
prefetch_related该方法内部其实就是子查询
    将子查询查询出来的所有结果也给你封装到对象中
    给你的感觉好像也是一次性搞定的
"""
for i in res:
    print(i.publish.name)

```