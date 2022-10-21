---
title: 03 Django高级
date: 2021-03-10 03:17:11.0
updated: 2021-11-09 03:55:26.478
url: /?p=9
categories: 
- 后端
tags: 
- Python
- django
---

## choice参数(数据库字段设计常见)

```Python
用户表
	性别
    学历
    工作经验
    是否结婚
    客户来源
    ...

class User(models.Model):
    username = models.CharField(max_length=32)
    age = models.IntegerField()
    gender_choices = (
        (1, '男'),
        (2, '女')
    )
    # 字段类型的选择取决于上面定义对应关系时的第一个变量
    gender = models.IntegerField(choices=gender_choices)
    """
    gender字段存储的还是数字 但是如果存储的狮子在上面元组的列举范围之内
    那么可以非常轻松的或得到数字对应的真正内容
    
    1. 如果gender字段存的狮子不在上述元组列表的范围时
        存的时候 没有列举出来的数字还是根据存储的字段类型决定,依旧会存储在内
    2. 如果在 如何获取到对应的中文信息
        只要是choices参数的字段 如果你想要获取对应的信息 固定写法 get_字段名_display()
        如果没有对应关系,那么存储的字段内容是什么就返回什么
    """

```

## MTV与MVC模型(了解)

```Python
# MTV: django号称是mtv模型
M: models
T: templates
V: views
# MVC: 其实django本质上还是mvc
M: models
V: views
C: controller
    
# vue框架: MVVM模型
```

## 多对多三种创建方式

```Python
# 全自动:	利用orm自动帮助我们创建第三张关系表
class Book(models.Model):
    name = models.CharField(max_length=32)
    authors = models.ManyToManyField(to='Author')

class Author(models.Model):
    name = models.CharField(max_length=32)
"""
有点: 代码不需要自己写 非常的方便 还支持orm提供操作第三张表的方法
缺点: 第三张表的扩展性比较差(没有办法额外添加字段)
"""
# 全手动(不建议)
class Book(models.Model):
    name = models.CharField(max_length=32)

class Author(models.Model):
    name = models.CharField(max_length=32)

class Book2Author(models.Model):
    book_id = models.ForeignKey(to='Book')
    author_id = models.ForeignKey(to='Author')
"""
优点: 第三张表完全取决于你自己进行额外的扩展
不足: 需要写的代码比较多 不能使用orm提供的简单的方法
"""
# 半自动
class Book(models.Model):
    name = models.CharField(max_length=32)
    authors = models.ManyToManyField(to='Author',
                                     through='Book2Author',
                                     through_fields=('book', 'author'))

class Author(models.Model):
    # name = models.CharField(max_length=32)
    # book = models.ManyToManyField(to='Author',
    #                                  through='Book2Author',
    #                                  through_fields=('author', 'book'))
# through_fields的顺序当前表是谁 就把对应的字段放在前面
class Book2Author(models.Model):
    book_id = models.ForeignKey(to='Book')
    author_id = models.ForeignKey(to='Author')
"""
可以使用orm的正反向查询,但是没法使用add,set,remove,clear这四个方法
"""
```

> 为了拓展性更高, 我们一般都会使用半自动

## Ajax(Asynchronous JavaScript And XML)

```Python
"""
异步提交
局部刷新
例子: GitHub注册
	动态获取用户名实时的跟后端确认并实时的展示到前端

朝后端发送请求的方式:
	1. 浏览器地址栏直接输入url回车	GET请求
	2. a标签href属性	GET请求
	3. form表单     	GET请求/POST请求
	4. ajax			 GET请求/POST请求
	
Ajax不是新的编程语言, 而是一种使用现有标准的方法(相当于装饰器)
Ajax最大的优点是在不加在整个页面的情况下, 可以与服务器交换数据并更新不跟网页内容

Ajax我们只学习jQuery封装之后的版本(不学原生的 原生的复杂并且在实际项目中一般不使用)
所以我们在前端页面使用ajax的时候要确保导入了jQuery
ps: 并不是只有jQuery能够实现ajax, 其他框架也可以 但是换汤不换药 原理都是一样的
"""
```

### 小案例

```Python
"""
页面上有三个input框
	在前两个框中输入数字 点击按钮 朝后端发送ajax请求
	后端计算出结构 再返回给前端动态展示的第三个input框中
	(整个过程不允许刷新,也不能在前端计算)
"""
# 前端
// 先给按钮绑定一个点击事件
    $('#btn').click(function(){
        // 朝后端发送ajax请求
        $.ajax({
            // 1. 指定朝那个后端发送ajax请求
            url:'', // 不写就是朝当前地址提交
            // 2. 请求方式
            type:'post', // 不指定默认为get 都是小写
            // 3. 数据
            data:{'digit1':$('#d1').val(),'digit2':$('#d2').val()},
            // 4. 回调函数(异步回调机制) 当后端给你返回结果的时候会自动触发 args接受后端的返回结果
            {#dateType:true, //会自动将后端返回的字符串序列化#}
            success:function(args){
                {#$('#d3').val(args);#}
                console.log(typeof args);
                console.log(args.msg);
            }
        })
    })
            
# 后端
def ab_ajax(request):
    if request.method == 'POST':
        first_digit = int(request.POST.get('digit1'))
        second_digit = int(request.POST.get('digit2'))
        result = first_digit + second_digit
        d = {'code': 200, 'msg': result}
        return JsonResponse(d)
    return render(request, 'index.html')

```

> 针对后端如果是用HttpResponse返回的数据 回调函数不会自动反序列化
>
> 如果后端直接使用JsonResponse返回的数据 会叫函数不会自动反序列化
>
> HTTPResponse解决方式
>
> 	1. 自己在前端利用JSON.parse()
> 	2. 在ajax里面配置一个参数

## 前后端传输数据的编码格式(contentType)

```Python
# 我们主要研究post请求数据的编码格式
"""
get请求数据及时直接放在url后面
url?username=张三&password=123
"""
# 可以向后端发送post请求的方法:
"""
1. form表单
2. ajax请求
"""

前后端传输数据的编码格式
urlencoded
formdata
json

# 研究form表单
默认的数据编码格式是: urlencoded
数据格式: username=zhangsan&password=123
django后端针对符合urlencoded编码格式的数据都会自动解析封装到request.POST中

如果你把编码格式改为formdate, 那么针对普通的键值对还是解析到request.POST中,而文件解析到request.FILES中

form表单是无法发送json格式的数据的

# 研究ajax
默认的编码格式: urlencoded
数据格式: username=zhangsan&password=123
```

## ajax发送json格式数据

```Python
"""
前后端传输数据的时候一定要确保编码格式跟数据真正的格式是一致的
不要骗人家!!!
{"username":"zhangsan","password":123} ==> 在request.POST里面肯定拿不到
"""
$('#d1').click(function(){
        $.ajax({
            url:'',
            type:'post',
            data:JSON.stringify({'username':'zhangsan','password':123}),
            contentType:'application/json',
            success:function(){

            }
        })
    })


request对象方法补充: 
    request.is_ajax()
    判断当前请求是否为ajax请求 返回bool值
django对待json格式的数据不会做任何的处理,全部存储在request.body中,是一个二进制的json字符串
需要我们自己手动处理
json_bytes = request.body
json_str = json_bytes.decode('utf-8')
json_dict = json.loads(json_str)

# json_loads括号内如果传入一个二进制格式的数据,那么内部会自动解码,然后反序列化
json_dict = json.loads(json_bytes)
```

> 注意: ajax发送json格式数据需要注意以下几点
>
> 1. contentType参数需要指定为: application/json
> 2. 数据是真正的json格式
> 3. django后端不会帮你处理json格式的数据,需要自己去request.body中获取并处理

## ajax发送文件

```Python
"""
ajax发送文件需要借助于js内置对象 FormData
"""
# 前端
// 点击按钮想后端发送普通键值对和文件数据
$('#d4').on('click',function(){
    // 1. 需要先利用FormData内置对象
    let formDataObj = new FormData();
    // 2. 添加普通的键值对
    formDataObj.append('username',$('#d1').val());
    formDataObj.append('password',$('#d2').val());
    // 3. 添加文件对象
    formDataObj.append('myfile',$('#d3')[0].files[0]);
    // 4. 将对象基于ajax发送给后端
    $.ajax({
        url:'',
        type:'post',
        data:formDataObj,
        //ajax发送文件必须指定两个参数
        contentType:false, // 不需要使用任何编码 django后端自动识别formdata对象
        processData: false, // 告诉浏览器不要对你的数据进行任何处理
        success: function ({

                           })
    })
})
# 后端 数据依旧存储在request.POST,request.FILES中
```

## django自带的序列化组件(drf做铺垫)

```Python
# 需求: 在前端获取到后端用户表里的所有数据 并且要是列表套字典格式
def ser(request):
    users = models.User.objects.all()
    # [{},{},{}]
    # user_list = []
    # for user in users:
    #     tmp = {
    #         'pk': user.pk,
    #         'username': user.username,
    #         'age': user.age,
    #         'gender': user.get_gender_display()
    #     }
    #     user_list.append(tmp)
    # return JsonResponse(user_list, safe=False)
    """
    [
        {"pk": 1, "username": "jason", "age": 18, "gender": "male"},
        {"pk": 2, "username": "egon", "age": 19, "gender": "female"},
        {"pk": 3, "username": "tank", "age": 17, "gender": "male"},
        {"pk": 4, "username": "kivin", "age": 20, "gender": 3}
    ]
    # 前后端分离项目
    作为后端的你只需要写代码并且将数据处理好
    能够序列化返回给前段即可
    再写一个接口文档
    告诉前端每一个字段代表什么意思即可
    """
    # 序列化
    res = serializers.serialize('json',users)
    return HttpResponse(res)
    """会自动帮你把数据变成json格式的字符串并且内部非常的全面"""
    """
  [{"model": "app01.user", "pk": 1, "fields": {"username": "jason", "age": 18, "gender": 1}}, {"model": "app01.user", "pk": 2, "fields": {"username": "egon", "age": 19, "gender": 2}}, {"model": "app01.user", "pk": 3, "fields": {"username": "tank", "age": 17, "gender": 1}}, {"model": "app01.user", "pk": 4, "fields": {"username": "kivin", "age": 20, "gender": 3}}]
    写接口的就是利用序列化组件 渲染数据然后写一个借口文档 该交代的交代一下就行了
    """
```

## 批量插入

```Python
# 批量插入 bulk_create()
book_list = []
for i in range(10000):
    book = models.Book(title='第%s本书'%i)
    book_list.append(book)
models.Book.objects.bulk_create(book_list)
books = models.Book.objects.all()
return render(request, 'ab_pi.html', locals())
```

## 自定义分页器

基于bootstrap实现,使用之前需要导入bootstrap

```Python
class Pagination(object):
    def __init__(self, current_page, all_count, per_page_num=10, pager_count=11):
        """
        封装分页相关数据
        :param current_page: 当前页
        :param all_count:    数据库中的数据总条数
        :param per_page_num: 每页显示的数据条数
        :param pager_count:  最多显示的页码个数
        """
        try:
            current_page = int(current_page)
        except Exception as e:
            current_page = 1

        if current_page < 1:
            current_page = 1

        self.current_page = current_page

        self.all_count = all_count
        self.per_page_num = per_page_num

        # 总页码
        all_pager, tmp = divmod(all_count, per_page_num)
        if tmp:
            all_pager += 1
        self.all_pager = all_pager

        self.pager_count = pager_count
        self.pager_count_half = int((pager_count - 1) / 2)

    @property
    def start(self):
        return (self.current_page - 1) * self.per_page_num

    @property
    def end(self):
        return self.current_page * self.per_page_num

    def page_html(self):
        # 如果总页码 < 11个：
        if self.all_pager <= self.pager_count:
            pager_start = 1
            pager_end = self.all_pager + 1
        # 总页码  > 11
        else:
            # 当前页如果<=页面上最多显示11/2个页码
            if self.current_page <= self.pager_count_half:
                pager_start = 1
                pager_end = self.pager_count + 1

            # 当前页大于5
            else:
                # 页码翻到最后
                if (self.current_page + self.pager_count_half) > self.all_pager:
                    pager_end = self.all_pager + 1
                    pager_start = self.all_pager - self.pager_count + 1
                else:
                    pager_start = self.current_page - self.pager_count_half
                    pager_end = self.current_page + self.pager_count_half + 1

        page_html_list = []
        # 添加前面的nav和ul标签
        page_html_list.append('''
                    <nav aria-label='Page navigation>'
                    <ul class='pagination'>
                ''')
        first_page = '<li><a href="?page=%s">首页</a></li>' % (1)
        page_html_list.append(first_page)

        if self.current_page <= 1:
            prev_page = '<li class="disabled"><a href="#">上一页</a></li>'
        else:
            prev_page = '<li><a href="?page=%s">上一页</a></li>' % (self.current_page - 1,)

        page_html_list.append(prev_page)

        for i in range(pager_start, pager_end):
            if i == self.current_page:
                temp = '<li class="active"><a href="?page=%s">%s</a></li>' % (i, i,)
            else:
                temp = '<li><a href="?page=%s">%s</a></li>' % (i, i,)
            page_html_list.append(temp)

        if self.current_page >= self.all_pager:
            next_page = '<li class="disabled"><a href="#">下一页</a></li>'
        else:
            next_page = '<li><a href="?page=%s">下一页</a></li>' % (self.current_page + 1,)
        page_html_list.append(next_page)

        last_page = '<li><a href="?page=%s">尾页</a></li>' % (self.all_pager,)
        page_html_list.append(last_page)
        # 尾部添加标签
        page_html_list.append('''
                                           </nav>
                                           </ul>
                                       ''')
        return ''.join(page_html_list)
```

## form组件

```Python
"""
写一个注册功能
	获取用户名和密码 利用form表单提交数据
	在后端判断用户名和密码是否符合一定的条件
		用户名不能含有金瓶梅
		密码不能少于三位数
	如果符合条件需要你讲提示信息展示到前端页面
"""
```

```Python
<form action="" method="post">
    <p>username: <input type="text" name="username">
    <span style="color: red">{{ back_dict.username }}</span>
    </p>
    <p>password: <input type="password" name="password">
    <span style="color: red">{{ back_dict.password }}</span>
    </p>
    <input type="submit" class="btn btn-primary">
</form>
"""
1. 手动书写前端获取用户数据的html代码		渲染html代码
2. 后端对用户数据的校验					校验数据
3. 对不符合要求的数据进行前端提示			展示提示信息

form组件
	能够完成的事情
	1. 渲染HTML代码
	2. 校验数据
	3. 展示提示信息
	
为什么数据校验一定要去后端	不能在前端利用js直接完成呢?
	数据校验前端可有可无
	但是后端必须要有!!!
	
	因为前端的校验是弱不禁风的 你可以直接修改
	或者利用爬虫程序绕过前端页面直接朝后端提交数据
"""
```

### form组件校验数据

```Python
# 基本使用
from django import forms
class MyForm(forms.Form):
    username = forms.CharField(min_length=3,max_length=8)
    password = forms.CharField(min_length=6,max_length=12)
    email = forms.EmailField()
```

```python 
"""
1. 测试环境的准备 可以拷贝代码准备
2. 其实pycharm里面已经准备好了一个测试环境 Python console
"""

from app01 import models
from app01 import views
# 1. 强带校验的数据组织成字典的形式传入即可
form_obj = views.MyForm({'username':'zhangsan','password':123,'email':'123'})
# 2. 判断数据是否合法 注意该方法只有在所有的字段都是合法的时候才会返回True
form_obj.is_valid()
False
# 3. 查看多有符合校验规则的数据
form_obj.cleaned_data
{'username': 'zhangsan'}
# 4. 查看所有不符合校验规则以及不符合的原因
form_obj.errors
{'password': ['Ensure this value has at least 6 characters (it has 3).'], 'email': ['Enter a valid email address.']}
# 5. 校验数据只校验定义的字段,多传入的字段不做校验
form_obj = views.MyForm({'username':'zhangsan','password':123567,'email':'123@qq.com','hobby':'study'})
form_obj.is_valid()
True
# 6. 校验数据 类里面所有的字段都必须传值
form_obj = views.MyForm({'username':'zhangsan','password':'123456'})
form_obj.is_valid()
False
```

> 也就意味着 默认情况下数据可以多传,但是不可以少

### forms组件 渲染标签

```Python
"""
forms组件只会自动帮你渲染获取用户输入的标签(input,select,radio,checkbox)
不会帮助你渲染按钮
"""
# 前端
<form action="" method="post">
    <p>第一种渲染方式:代码书写极少,封装程度太高 不利于后续得到扩展 一般情况下只在本地测试使用</p>
{#    {{ form_obj.as_p }}#}
{#    {{ form_obj.as_ul }}#}
{#    {{ form_obj.as_table }}#}
    <p>第二种渲染方式: 可扩展性很强,但是需要书写的代码太多 一般情况下不使用</p>
{#    {{ form_obj.username.label }}:{{ form_obj.username }}#}
    <p>第三种渲染方式: 推荐使用</p>
    {% for form in form_obj %}
        <p>{{ form.label }}:{{ form }}</p>
    {% endfor %}

</form>
# 后端
def index(request):
    # 1. 先产生一个空对象
    form_obj = MyForm()
    return render(request,'index.html',locals())
    
```

> label属性默认战士的是类中定义的字段的首字母大写的形式
>
> 也可以自己修改,需要在字段后面添加label属性

### forms组件 展示提示信息

```Python
"""
浏览器还自动帮你校验数据 但是前端的校验弱不禁风
如何让浏览器不做校验
需要在form表单中添加属性  
"""
def index(request):
    # 1. 先产生一个空对象
    form_obj = MyForm()
    if request.method == 'POST':
        # 1. 获取用户数据并且校验
        # 2. 校验数据需要构造成字典的格式传入才行
        # ps: request.POST可以看成一个字典
        # 3. 校验数据
        form_obj = MyForm(request.POST)
        # 4.判断数据是否合法
        # 5. 如果合法 操作数据福存储数据
        if form_obj.is_valid():
            return HttpResponse('OK')
        # 5. 如果不合法 有错误
        else:
            pass
        
<form action="" method="post" novalidate>
    <p>第一种渲染方式:代码书写极少,封装程度太高 不利于后续得到扩展 一般情况下只在本地测试使用</p>
{#    {{ form_obj.as_p }}#}
{#    {{ form_obj.as_ul }}#}
{#    {{ form_obj.as_table }}#}
    <p>第二种渲染方式: 可扩展性很强,但是需要书写的代码太多 一般情况下不使用</p>
{#    {{ form_obj.username.label }}:{{ form_obj.username }}#}
    <p>第三种渲染方式: 推荐使用</p>
    {% for form in form_obj %}
        <p>{{ form.label }}:{{ form }}
        <span style="color: red">{{ form.errors.0 }}</span>
        </p>
    {% endfor %}
    <input type="submit" value="提交" class="btn btn-info">
</form>
```

> 注意: 
>
> 1. 必备的条件: get请求和post请求传给html页面对象变量名必须要一样
> 2. 2. forms组件当你的数据不合法的情况下 会保存你上一次的数据 让你基于原来的结果进行修改 更加的人性化
>
> 针对错误信息还可以自定义定制:
>
> ```Python
> password = forms.CharField(min_length=6, max_length=12, label='密码', error_messages={
>         'min_length': '密码至少三位',
>         'max_length': '密码最多八位',
>         'required': '密码不能为空'
>     })
> ```

### 钩子函数(HOOK)

在特定的节点自动触发完成响应操作,钩子函数在forms组件中相当于第二道关卡, 能够让我们自定义校验规则

在forms组件内有两种钩子:

+ 局部钩子

  当你需要给某个字段增加校验规则的时候可以使用

+ 全局钩子

  让你需要给多个字段增加校验规则的时候可以使用

实际案例:

```Python
# 1. 校验用户名汇总不能含有666		只校验username字段	局部钩子


# 2. 校验密码和确认密码是否一致		校验password和repassword两个字段	全局钩子
```

### forms组件其它参数以及补充知识点

```Python
label: 字段名
error_messages: 自定义报错信息
initial: 相当于form标签里的value,提供默认值
required: 控制字段是否必填
"""
1. 字段没有样式
2. 针对不同的input如何修改?
	text
	password
	date
	radio
	checkbox
	...
"""
widget=forms.widgets.TextInput(attrs={'class':'form-control'})
widget=forms.widgets.PasswordInput(attrs={'class':'form-control'})

还支持正则校验
validators=[
            RegexValidator(r'^1(3\d|4[5-9]|5[0-35-9]|6[2567]|7[0-8]|8\d|9[0-35-9])\d{8}$','手机号格式不正确'),
        ]
```

```Python
# 其他类型渲染
# radio
gender = forms.ChoiceField(
    choices=((1,'male'),(2,'female'),(3,'secret')),
    label='性别',
    initial=3,
    widget=forms.widgets.RadioSelect()
)
# select
hobby = forms.ChoiceField(
    choices=((1,'篮球'),(2,'足球'),(3,'双色球')),
    label='爱好',
    initial=3,
    widget=forms.widgets.Select()
)
# 多选select
hobby1 = forms.ChoiceField(
    choices=((1, '篮球'), (2, '足球'), (3, '双色球')),
    label='爱好',
    initial=[1,3],
    widget=forms.widgets.SelectMultiple()
)
# checkbox
keep = forms.ChoiceField(
    label='是否记住密码',
    initial='checked',
    widget=forms.widgets.CheckboxInput()
)
# 多选checkbox
hobby2 = forms.ChoiceField(
    choices=((1, '篮球'), (2, '足球'), (3, '双色球')),
    label='爱好',
    initial=[1, 3],
    widget=forms.widgets.CheckboxSelectMultiple()
)
```

### forms组件源码

## cookie与session

### 简介

```Python
"""
发展史:
	1. 网站都没有保存用户功能的需求	所有用户访问返回的结果都是一样的
		eg: 新闻,博客...
	2. 出现了一些需要保存用户信息的网站
		eg: 淘宝,支付宝...
    以登录功能为例: 如果不保存用户登录状态 也就意味着用户每次访问网站都需要重复输入用户名和密码
    当用户第一次登陆之后 将用户的用户名和密码返回给用户浏览器 让用户浏览器保存在本地,之后访问网站的时候浏览器自动将保存在浏览器上的用户名和密码发送给服务端,服务端获取之后进行验证
    早起这种方式具有非常大的安全隐患
    
    优化: 
    	当用户登陆成功之后,服务端产生一个随机字符串(在服务端保存数据,用kv键值对的形式),交由客户端保存
    	之后访问服务端的时候都带着该随机字符串,服务端去数据库中对比是否有对应的随机字符串从而获取对应的用户信息
    	
    但是,如果你拿到了截获到的随机字符串,那么你就可以充当当前用户 其实还是存在安全隐患的
    要知道的是,在web领域没有绝对安全的安全,也没有绝对的不安全
"""
cookie
	服务端保存在客户端浏览器删的信息都称之为cookie
    他的表现形式为kv键值对
session
	数据是保存在服务端的并且它的表现形式也是kv键值对
token
	session虽然是保存在服务端的,但是禁不住数据量大
    服务端不在保存数据
    	登陆成功之后 将一段数据进行加密处理
jwt认证
	三段信息
    (后期)
总结:
    1. cookie就是保存在客户端浏览器上的信息
    2. session就是保存在服务端上的信息
    3. session是基于cookie工作的
```

### cookie操作

```Python
# 视图函数的返回值
return render()
return HttpResponse()
return redirect()

obj1 = render()
obj2 = HttpResponse()
obj3 = redirect()
return obj1
...
# 如果你想要操作cookie, 就必须要写下面的这种方式obj对象

"""
设置cookie
obj.set_cookie(key,value)

获取cookie
request.COOKIES.get(key)

max_age
expires
	两者都是设置超时的参数,都是以秒为单位的
	ie浏览器使用expires

delete_cookie() 用来删除cookie
"""

def login_auth(func):
    def inner(request,*args,**kwargs):
        # print(request.path_info)
        # print(request.get_full_path())  # 能够获取到用户上一次想要访问的url
        target_url = request.get_full_path()
        if request.COOKIES.get('username'):
            return func(request,*args,**kwargs)
        else:
            return redirect('/login/?next=%s'%target_url)
    return inner

def login(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
        if username == 'jason' and password == '123':

            # 获取用户上一次想要访问的url
            target_url = request.GET.get('next')  # 这个结果可能是None
            if target_url:
                obj = redirect(target_url)
            else:
                # 保存用户登陆状态
                obj = redirect('/home/')
            # 让浏览器记录cookie数据
            obj.set_cookie('username', 'jason666')
            """
            浏览器不单单会帮你存
            而且后面每次访问你的时候还会带着它过来
            """
            # 跳转到一个需要用户登陆之后才能看的页面
            return obj
    return render(request,'login.html')


@login_auth
def home(request):
    # 获取cookie信息 判断你有没有
    # if request.COOKIES.get('username') == 'jason666':
    #     return HttpResponse("我是home页面，只有登陆的用户才能进来哟~")
    # # 没有登陆应该跳转到登陆页面
    # return redirect('/login/')
    return HttpResponse("我是home页面，只有登陆的用户才能进来哟~")

@login_auth
def logout(request):
    obj = redirect('/login/')
    obj.delete_cookie('username')
    return obj

```

### session操作

```Python
"""
session数据是保存在服务端的,给客户端返回的是一个随机字符串
	sessionid:随机字符串
1. 在默认情况下操作session的时候需要django默认的一张django_session表
django默认的session的过期时间是14天
	 
设置session
request.session['key'] = value

获取session
request.session.get('key')
"""
# django_session表中的数据条数是取决于浏览器的 同一个计算机上的同一个浏览器只会有一条数据生效
# 主要是为了节省服务端资源

# 设置过期时间
request.session.set_expiry()
	括号内可以存放四种类型参数
    1. 整数	秒
    2. 实践对象		到指定的日期就失效
    3. 0	浏览器一关闭就立刻失效
    4. 不写	默认14天
    
# 清楚session
request.session.delete() # 删除当前会话所有的session 只删除服务端
request.session.flush() # 浏览器和服务端都清空(常用)

# session是保存在服务端的,但是session保存的位置可以有多种选择
1. mysql
2. 文件
3. redis
4. memcache
...
```

## CBV如何添加装饰器

```Python
from django.views import View
from django.utils.decorators import method_decorator

"""
cbv中django不建议你直接给类方法直接添加装饰器
无论装饰器能否正常使用
"""


# @method_decorator(login_auth,name='get') # 方式二(可以添加多个针对不同的方法加不同的装饰器)
# @method_decorator(login_auth,name='post')
class MyLogin(View):
    @method_decorator(login_auth)  # 方式三: 他会直接作用于当前类里面所有的方法
    def dispatch(self, request, *args, **kwargs):
        pass

    # @method_decorator(login_auth) # 方式一: 指名道姓
    def get(self, request):
        return HttpResponse('get请求')

    def post(self, request):
        return HttpResponse('post请求')

```

## django中间件

首先 django自带七个中间件, 每个中间件都是各自对应的功能

并且django还支持程序员自定义中间件

你在用django开发项目的项目的时候,只要是涉及到全局相关写功能都可以使用中间件方便的完成

+ 全局用户身份校验
+ 全局用户权限校验
+ 全局访问频率校验
+ ...

```Python
"""
django中间件是django的门户
1. 请求来的时候需要先经过中间件才能真正的到达django后端
2. 响应走的时候最后也需要经过中间件才能发送出去
"""
```

### django请求生命周期流程图

![image-20210701071459432](C:\Users\evans\Documents\Typora\web-backend-learning\03 Django高级.assets\image-20210701071459432.png)

```Python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

class SessionMiddleware(MiddlewareMixin):
    def process_request(self, request):
        session_key = request.COOKIES.get(settings.SESSION_COOKIE_NAME)
        request.session = self.SessionStore(session_key)
    def process_response(self, request, response):
        return response
      
class CsrfViewMiddleware(MiddlewareMixin):
  	def process_request(self, request):
        csrf_token = self._get_token(request)
        if csrf_token is not None:
            # Use same token next time.
            request.META['CSRF_COOKIE'] = csrf_token
    def process_view(self, request, callback, callback_args, callback_kwargs):
        return self._accept(request)

    def process_response(self, request, response):
        return response
      
class AuthenticationMiddleware(MiddlewareMixin):
    def process_request(self, request):
        request.user = SimpleLazyObject(lambda: get_user(request))
"""
django支持程序员自定义中间件并且暴露给程序员五个可以自定义的方法
	1.必须掌握
		process_request
			
		process_response
	2.了解即可
		process_view
			
		process_template_response
		
		process_exception
"""
```

### 如何自定义中间件

```Python
from django.utils.deprecation import MiddlewareMixin


class MyMiddleware(MiddlewareMixin):
    def process_request(self, request):
        print('我是第一个自定义中间件里面的process_request方法')
    #
    # def process_response(self,request):
    #     pass
class MyMiddleware1(MiddlewareMixin):
    def process_request(self, request):
        print('我是第二个自定义中间件里面的process_request方法')

class MyMiddleware2(MiddlewareMixin):
    def process_request(self, request):
        print('我是第三个自定义中间件里面的process_request方法')


MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'app01.mymiddleware.mymiddleware.MyMiddleware',
    'app01.mymiddleware.mymiddleware.MyMiddleware1',
    'app01.mymiddleware.mymiddleware.MyMiddleware2',
]
```

```Python

# django支持程序员自定义中间件并且暴露给程序员五个可以自定义的方法
1.必须掌握
    process_request 最常用
        1 请求来的时候经过每一个中间件里面的process_request方法
        结果的顺序是按照配置文件中注册的中间件顺序来的
        2 如果中间件里面没有定义该方法,那么直接跳过执行下一个中间件
        3 如果该方法返回了HTTPResponse对象,那么请求将不会继续往后执行,而是原路返回(相当于校验失败不允许访问...)

        所以process_request方法就是用来做全局相关的所有限制功能
    process_response
         1 响应走的时候需要经过每一个中间件里的该方法
        该方法有两个参数request,response
        2 该方法必须返回一个HTTPResponse对象
            2.1 默认返回response
            2.2 你也可以返回自己定义的
         3 顺序是按照配置文件中注册的中间件从下至上依次执行, 如果你没有定义的话,那么直接跳过

     研究如果在第一个process_request方法就已经返回了HTTPResponse对象,那么响应走的时候是经过所有的中间件里面的process_response还是其他情况?
     是其他情况: 直接走同级别的process_response,然后原路返回


     flask框架也有一个中间件但是他的规律不一样: 只要返回数据了就必须经过所有中间件里面类似于process_response方法
```

```Python
2.了解即可
		process_view
			在路由匹配成功之后执行视图函数之前,会自动中间件里的方法
        	def process_view(self,request,view_name,*args,**kargs)
		process_template_response
			返回的HTTPResponse对象有render属性的时候才会触发
		process_exception
        	def process_exception(self,request,exception)
            当视图函数出现异常的情况下触发
```

### csrf跨站请求伪造

```Python
"""
钓鱼网站
	我搭建一个跟正规网站一模一样的界面(中国银行)
	用户不小心进入到我们的网站,用户给某个人
	打钱的操作确确实实是提交给中国银行的系统,用户的钱也确确实实减少了
	但是唯一不同的时候打钱的账户不使用用户要打钱的账户变成了其他人的账户
	
内部本质
	我们在钓鱼网站的页面 针对对方账户 只给用户提供一个没有name属性的普通input框
	然后我们在内部隐藏一个已经写好了的name和value的input框
	
如何规避上述问题
	csrf跨站请求伪造
	网站在给用户返回一个具有提交数据功能页面的时候会给这个页面加一个唯一标识
	当这个页面朝后端发送post请求的时候 我的后端会先校验唯一标识,如果唯一标识不对直接拒绝(403 forbidden)如果正确则正常执行
"""
```

### 如何符合校验

```Python
# 模板语言 form表单如何符合校验
<form action="" method="post" class="col-md-10">
        {% csrf_token %}
        <p>username: <input type="text" name="username" class="form-control"></p>
        <p>target_user: <input type="text" name="target_user" class="form-control"></p>
        <p>money: <input type="text" name="money" class="form-control"></p>
        <input type="submit">
    </form>
    
```

```js
// ajax如何符合校验
$('#d1').click(function(){
        $.ajax({
            url:'',
            type:'post',
            // 第一种 利用标签查找获取页面上的随机字符串
            {#data:{'username':'zhangsan','csrfmiddlewaretoken':$('[name="csrfmiddlewaretoken"]').val()},#}
            // 第二种 使用模板语法
            {#data:{'username':'zhangsan','csrfmiddlewaretoken':'{{ csrf_token }}'},#}
            // 第三种 通用方式 只需要引用js文件
            data:{'username':'zhangsan'},
            success:function (){
            }
        })
    })



function getCookie(name) {
    var cookieValue = null;
    if (document.cookie && document.cookie !== '') {
        var cookies = document.cookie.split(';');
        for (var i = 0; i < cookies.length; i++) {
            var cookie = cookies[i].trim();
            // Does this cookie string begin with the name we want?
            if (cookie.substring(0, name.length + 1) === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}
var csrftoken = getCookie('csrftoken');

function csrfSafeMethod(method) {
    // these HTTP methods do not require CSRF protection
    return (/^(GET|HEAD|OPTIONS|TRACE)$/.test(method));
}
$.ajaxSetup({
    beforeSend: function(xhr, settings) {
        if (!csrfSafeMethod(settings.type) && !this.crossDomain) {
            xhr.setRequestHeader("X-CSRFToken", csrftoken);
        }
    }
});
```

### csrf装饰器

```Python
"""
1. 网站整体都不校验csrf,就单单几个视图函数需要校验

2. 网站整体都校验csrf,就单单几个视图函数不校验
"""
# 当settings中注释掉csrf后,再views函数上面添加
@csrf_protect	校验
# 当settings中开启csrf后,在views函数上面添加
@csrf_exempt	不校验

# 针对CBV的装饰器三种方式
csrf_protect 三种方式都可以
	# 第一种
    @method_decorator(csrf_protect)
    def post(self):
        pass
    # 第二种
    @method_decorator(csrf_protect,name='post')
    class MyCsrfToken(View):
        pass
    # 第三种
    @method_decorator(csrf_protext)
    def dispatch(self,request,*args,**kwargs):
        return super(MyCsrfToken,self).dispatch(request,*args,**kwargs)
    
csrf_exempt 只有唯一一种方式可以 那就是上面的第三种方式
	@method_decorator(csrf_exempt)
    def dispatch(self,request,*args,**kwargs):
        return super(MyCsrfToken,self).dispatch(request,*args,**kwargs)
    
```

## 补充知识点

```Python
# 模块:importlib
import importlib
res = 'myfile.b'
ret = importlib.import_module(res)  # from myfile import b
# 该方法最小只能到py文件名
print(ret)
```

### 重要思想

```Python
import settings
import importlib


def send_all(content):
    for path_str in settings.NOTIFY_LIST:  #'notify.email.Email'
        module_path,class_name = path_str.rsplit('.',maxsplit=1)
        # module_path = 'notify.email'  class_name = 'Email'
        # 1 利用字符串导入模块
        module = importlib.import_module(module_path)  # from notify import email
        # 2 利用反射获取类名
        cls = getattr(module,class_name)  # Email、QQ、Wechat
        # 3 生成类的对象
        obj = cls()
        # 4 利用鸭子类型直接调用send方法
        obj.send(content)
```

## Auth模块

```Python
"""
其实我们在创建好一个django项目之后执行数据库迁移命令会自动生成很多表
	django_session
	auth_user
django在启动之后就可以直接访问admin路由,需要输入用户名和密码,数据参考的就是auth_user表,并且还必须是管理员用户才能进入
"""
# 创建超级用户(管理员)
Python3 manage.py createsuperuser

# 依赖auth_user表完成相关所有功能
```

### 方法总结

```Python
# 1.比对用户名和密码是否正确
user_obj = auth.authenticate(request,username=username,password=password)
# 括号内必须同时传入用户名和密码
print(user_obj)  # 用户对象  jason   数据不符合则返回None
print(user_obj.username)  # jason
print(user_obj.password)  # 密文

# 2.保存用户状态
auth.login(request,user_obj)  # 类似于request.session[key] = user_obj
# 主要执行了该方法 你就可以在任何地方通过request.user获取到当前登陆的用户对象

# 3.判断当前用户是否登陆
request.user.is_authenticated()

# 4.获取当前登陆用户
request.user

# 5.校验用户是否登陆装饰器
from django.contrib.auth.decorators import login_required
# 局部配置
@login_required(login_url='/login/') 
# 全局配置
LOGIN_URL = '/login/'
	1.如果局部和全局都有 该听谁的?
    局部 > 全局
	2.局部和全局哪个好呢?
    全局的好处在于无需重复写代码 但是跳转的页面却很单一
    局部的好处在于不同的视图函数在用户没有登陆的情况下可以跳转到不同的页面

# 6.比对原密码
request.user.check_password(old_password)

# 7.修改密码
request.user.set_password(new_password)  # 仅仅是在修改对象的属性
request.user.save()  # 这一步才是真正的操作数据库

# 8.注销
auth.logout(request) 

# 9.注册
# 操作auth_user表写入数据
User.objects.create(username=username,password=password)  # 写入数据  不能用create 密码没有加密处理
# 创建普通用户
User.objects.create_user(username=username,password=password)
# 创建超级用户(了解):使用代码创建超级用户 邮箱是必填的 而用命令创建则可以不填
User.objects.create_superuser(username=username,email='123@qq.com',password=password)
```

### 如何扩展auth_user表

```Python
# 第一种 一对一关系表
from django.contrib.auth.models import User
class UserDetail(models.Model):
    phone = models.BigIntegerField()
    user = models.OneToOneField(to='User')


# 第二种 面向对象的继承
from django.contrib.auth.models import AbstractUser
class UserInfo(AbstractUser):
    """
    如果继承了AbstractUser
    那么执行数据库迁移的时候auth_user表将不会再被创建出来了
    而UserInfo表中会出现auth_user表中的所有字段以及自己扩展的字段
    好处: 在于你能够直接点击你自己的表更加快速的完成操作和扩展
    
    前提: 
        1. 在继承之前没有执行数据库迁移命令
            auth_user没有被创建,如果当前库已经创建了,就需要重新换一个database
        2. 继承的类里面的不要覆盖AbstractUser里面的字段名
            表里面有的字段不要动,只扩展外的字段
        3. 需要在配置文件中告诉django,你需要使用UserInfo替代auth_user
            AUTH_USER_MODEL = 'app01.UserInfo'
                                应用名.表名
    """
    phone = BigIntegerField()
    """
    你如果自己写表替代了auth_user那么,auth模块的功能还是照常使用
    """
```