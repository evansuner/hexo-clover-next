---
title: 05 Django-rest framework
date: 2021-03-14 03:17:13.0
updated: 2021-11-09 08:19:11.139
url: /?p=11
categories: 
- 后端
tags: 
- Python
- django
- drf
---

> 学习内容:restful规范,drf入门,序列化(最重要),响应,权限,认证,频率,过滤,分页

# Day One

## 1. web开发模式

+ ### 前后端混合开发(前后端不分离)

+ ### 前后端分离开发

## 2. Restful规范(10条)

抓包工具: fildder, Charles

规范:

+ 数据的安全规范: url链接一般都采用https协议进行传输

  > 采用https协议可以提高数据交互过程中的安全性

+ 接口特征表现, 一看就知道是一个api接口

  + 用api关键字标识api接口

    https://api.baidu.com

    https://www.baidu.com/api

+ 多数据版本共存

  + 在url链接中标识数据版本

    https://api.baidu.com/v1

    https://www.baidu.com/api/v1

  > url链接中路那个的v1,v2就是不同数据版本的体现(只有在一种数据资源有多个版本的情况下)

+ 数据即是资源,均使用名词(可复数) \*****

  + 接口一般都是完成前后台数据的交互,交互的数据我们称之为资源

    https://api.baidu.com/users

    https://api.baidu.com/books

  > 一般提倡用资源的复数形式,在url连接中尽量不要使用动词,错误示范: https://api.baidu.com/delete_user

  + 特殊的接口可以出现动词,因为这些接口一般没有一个明确的资源,或者是动词就是接口的核心含义

    https://api.baidu.com/place/search

    https://api.baidu.com/login

+ 资源操作由请求方式决定(method)  \*****

  + 操作资源一般都会涉及到增删改查,我们提供请求方法来表示增删改查动作

    https://api.baidu.com/books	get请求: 获取所有书的json数据

    https://api.baidu.com/books/1	get请求: 获取书的主键值为1的json数据

    https://api.baidu.com/books	post请求:新增一本书

    https://api.baidu.com/books/1	put请求: 整体修改主键为1的书的数据

    https://api.baidu.com/books/1	patch请求: 局部修改主键为1的书的数据

    https://api.baidu.com/books/1	delete请求: 删除主键为1的书的数据

+ 过滤,通过在url上传参的形式传递搜索条件 \*****

  https://api.baidu.com/books?limit=10

  https://api.baidu.com/books?offset=10

  https://api.baidu.com/books?page=2&per_page=100

  https://api.baidu.com/books?sortby=name&order=asc

  https://api.baidu.com/books?class_id=1

+ 响应状态码(status)

  + 正常响应 2xx
  + 重定向响应 3xx
  + 客户端异常 4xx
  + 服务端异常 5xx

+ 错误处理,应返回错误信息,error作为key,或者是message

+ 返回结果,针对不同的操作,服务器向用户返回的结果应该符合以下规范

  + GET /collections : 返回资源对象的列表(数组)
  + GET /collections/resource: 返回单个资源对象
  + POST /collections: 返回新生成的资源对象
  + PUT /collections/resource: 返回完整的资源对象
  + PATCH /collections/resource: 返回完整的资源对象
  + DELETE /collections/resource: 返回一个空文档

+ 需要url请求的资源需要访问自愿的请求链接

## 3. 简单实用drf

```python 
# 安装
pip install djangorestframework

# 使用 
## 1. 在INSTALLED_APPS中注册
INSTALLED_APPS = [
    'rest_framework',
]

## 2. 在models.py中创建一个模型表
class Book(models.Model):
    name = models.CharField(max_length=32, verbose_name='书名')
    price = models.DecimalField(max_digits=5, decimal_places=2)
    author = models.CharField(max_length=32)
    
## 3. 新建一个序列化类
from rest_framework.serializers import ModelSerializer
from app01.models import Book
class BookModelSerializer(ModelSerializer):
    class Meta:
        model = Book
        fields = "__all__"
        
## 4. 在视图中创建一个视图类
from rest_framework.viewsets import ModelViewSet
from app01.models import Book
from app01.ser import BookModelSerializer
class BookViewSet(ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookModelSerializer
    
## 5. 定义路由
from django.contrib import admin
from django.urls import path
from rest_framework.routers import DefaultRouter
from app01 import views
router = DefaultRouter()  # 可以处理视图的路由器
router.register('book', views.BookViewSet)  # 向路由器中注册视图集
urlpatterns = [
    path('admin/', admin.site.urls),
]
urlpatterns += router.urls  # 将路由器中的路由信息追加到django路由列表中

## 6. 启动,在postman或浏览器中测试
```



## 4. CBV源码

+ 视图类, 必须继承VIew(读View的源码)
+ 在类里面写get, post方法就可以了,只要get请求来(方法和fbv写法完全一样)
+ 路由的方法有点区别: views.类名.as_view()
+ 请求来了之后,路由匹配上 --> view(request) --> self.dispatch(request,*args,**kwargs)
+ dispatch将请求方法转成小写 –> 通过反射,去对象中找,没有找到,就加括号执行,并把request传进去

## 5. APIVIew源码分析(继承View并扩展,drf提供的)

+ 视图类,继承了ApiView
+ 在类里面写get, post方法就可以了,只要get请求来(方法和fbv写法完全一样)
+ 路由: views.Book.as_view()    这个as_view重写了View中的as_view方法, 并且处理了csrf,所有的请求都没有csrf校验了
+ 请求来了之后,路由匹配上 --> view(request) --> self.dispatch(request,*args,**kwargs),现在的dispatch方法不再是View中的dispatch了,而是来源于APIView



# Day Two

## 1. 序列化组件介绍

+ 序列化,序列化器会把模型对性转换成字典,经过response以后变成json字符串
+ 反序列化,把客户端发送过来的数据,经过request以后变成字典,序列化器可以把字典转成模型
+ 反序列化,完成数据校验

注意: 

![image-20210710161910536](C:\Users\evans\Documents\Typora\web-backend-learning\05 Django-rest framework.assets\image-20210710161910536.png)

遇到这样的问题 需要将rest_framework在app中注册一下

## 2. 简单使用

+ 写一个序列化类,继承serializers.Serializer
+ 在类中写模型中需要序列化的字段
+ 在视图类中,导入并实例化序列化类的对象,将需要序列化的对象传入
+ 序列化类的对象.data    是一个字典
+ 将字典返回,可以使用Response(序列化对象.data),但是需要在app中注册`"rest-framework"`(推荐), 也可以使用JsonResponse(序列化对象.data) ,不需要注册`"rest-framework"`

## 3. 序列化类常用字段类型

<table>
<thead>
<tr>
<th>字段</th>
<th>字段构造方式</th>
</tr>
</thead>
<tbody><tr>
<td><strong>BooleanField</strong></td>
<td>BooleanField()</td>
</tr>
<tr>
<td><strong>NullBooleanField</strong></td>
<td>NullBooleanField()</td>
</tr>
<tr>
<td><strong>CharField</strong></td>
<td>CharField(max_length=None, min_length=None, allow_blank=False, trim_whitespace=True)</td>
</tr>
<tr>
<td><strong>EmailField</strong></td>
<td>EmailField(max_length=None, min_length=None, allow_blank=False)</td>
</tr>
<tr>
<td><strong>RegexField</strong></td>
<td>RegexField(regex, max_length=None, min_length=None, allow_blank=False)</td>
</tr>
<tr>
<td><strong>SlugField</strong></td>
<td>SlugField(max<em>length=50, min_length=None, allow_blank=False) 正则字段，验证正则模式 [a-zA-Z0-9</em>-]+</td>
</tr>
<tr>
<td><strong>URLField</strong></td>
<td>URLField(max_length=200, min_length=None, allow_blank=False)</td>
</tr>
<tr>
<td><strong>UUIDField</strong></td>
<td>UUIDField(format=’hex_verbose’)  format:  1) <code>'hex_verbose'</code> 如<code>"5ce0e9a5-5ffa-654b-cee0-1238041fb31a"</code>  2） <code>'hex'</code> 如 <code>"5ce0e9a55ffa654bcee01238041fb31a"</code>  3）<code>'int'</code> - 如: <code>"123456789012312313134124512351145145114"</code>  4）<code>'urn'</code> 如: <code>"urn:uuid:5ce0e9a5-5ffa-654b-cee0-1238041fb31a"</code></td>
</tr>
<tr>
<td><strong>IPAddressField</strong></td>
<td>IPAddressField(protocol=’both’, unpack_ipv4=False, **options)</td>
</tr>
<tr>
<td><strong>IntegerField</strong></td>
<td>IntegerField(max_value=None, min_value=None)</td>
</tr>
<tr>
<td><strong>FloatField</strong></td>
<td>FloatField(max_value=None, min_value=None)</td>
</tr>
<tr>
<td><strong>DecimalField</strong></td>
<td>DecimalField(max_digits, decimal_places, coerce_to_string=None, max_value=None, min_value=None) max_digits: 最多位数 decimal_palces: 小数点位置</td>
</tr>
<tr>
<td><strong>DateTimeField</strong></td>
<td>DateTimeField(format=api_settings.DATETIME_FORMAT, input_formats=None)</td>
</tr>
<tr>
<td><strong>DateField</strong></td>
<td>DateField(format=api_settings.DATE_FORMAT, input_formats=None)</td>
</tr>
<tr>
<td><strong>TimeField</strong></td>
<td>TimeField(format=api_settings.TIME_FORMAT, input_formats=None)</td>
</tr>
<tr>
<td><strong>DurationField</strong></td>
<td>DurationField()</td>
</tr>
<tr>
<td><strong>ChoiceField</strong></td>
<td>ChoiceField(choices) choices与Django的用法相同</td>
</tr>
<tr>
<td><strong>MultipleChoiceField</strong></td>
<td>MultipleChoiceField(choices)</td>
</tr>
<tr>
<td><strong>FileField</strong></td>
<td>FileField(max_length=None, allow_empty_file=False, use_url=UPLOADED_FILES_USE_URL)</td>
</tr>
<tr>
<td><strong>ImageField</strong></td>
<td>ImageField(max_length=None, allow_empty_file=False, use_url=UPLOADED_FILES_USE_URL)</td>
</tr>
<tr>
<td><strong>ListField</strong></td>
<td>ListField(child=, min_length=None, max_length=None)</td>
</tr>
<tr>
<td><strong>DictField</strong></td>
<td>DictField(child=)</td>
</tr>
</tbody></table>


## 4. 序列化字段选项选项参数:

<p>选项参数</p>
<table>
<thead>
<tr>
<th>参数名称</th>
<th>作用</th>
</tr>
</thead>
<tbody><tr>
<td><strong>max_length</strong></td>
<td>最大长度</td>
</tr>
<tr>
<td><strong>min_length</strong></td>
<td>最小长度</td>
</tr>
<tr>
<td><strong>allow_blank</strong></td>
<td>是否允许为空</td>
</tr>
<tr>
<td><strong>trim_whitespace</strong></td>
<td>是否截断空白字符</td>
</tr>
<tr>
<td><strong>max_value</strong></td>
<td>最小值</td>
</tr>
<tr>
<td><strong>min_value</strong></td>
<td>最大值</td>
</tr>
</tbody></table>


<p>通用参数</p>
<table>
<thead>
<tr>
<th>参数名称</th>
<th>说明</th>
</tr>
</thead>
<tbody><tr>
<td><strong>read_only</strong></td>
<td>表明该字段仅用于序列化输出，默认False</td>
</tr>
<tr>
<td><strong>write_only</strong></td>
<td>表明该字段仅用于反序列化输入，默认False</td>
</tr>
<tr>
<td><strong>required</strong></td>
<td>表明该字段在反序列化时必须输入，默认True</td>
</tr>
<tr>
<td><strong>default</strong></td>
<td>反序列化时使用的默认值</td>
</tr>
<tr>
<td><strong>allow_null</strong></td>
<td>表明该字段是否允许传入None，默认False</td>
</tr>
<tr>
<td><strong>validators</strong></td>
<td>该字段使用的验证器</td>
</tr>
<tr>
<td><strong>error_messages</strong></td>
<td>包含错误编号与错误信息的字典</td>
</tr>
<tr>
<td><strong>label</strong></td>
<td>用于HTML展示API页面时，显示的字段名称</td>
</tr>
<tr>
<td><strong>help_text</strong></td>
<td>用于HTML展示API页面时，显示的字段帮助提示信息</td>
</tr>
</tbody></table>


## 5. 序列化组件修改数据

+ 写一个序列化的类,继承自serializers.Serializer

+ 在类中定义反序列化字段

+ 在视图类中使用,首先要实例化序列化的类,其次是将需要修改的对象传入,修改的数据传入

  ```
  student_ser = StudentSerializer(instance=student, data=request.data)
  ```

+ 数据校验

  ```
  student_ser.is_valid()
  ```

+ 如果验证通过就保存

  ```
  student_ser.save()
  ```

+ 如果验证不通过就按照自己的逻辑写

+ 如果校验字段不能完全实现你的校验功能,你还以通过局部钩子和全局钩子来实现校验

  这里的校验方式和forms组件不一样,forms组件使用clean_字段名(),这里使用validate_字段名

  ```Python
   # 局部钩子
      def validate_age(self, data):  # validate_字段名    接受一个参数
          # 如果年龄大于21岁就校验失败
          if not data > 21:
              return data
          raise ValidationError('年龄太大了')
  
      # 全局钩子
      def validate(self, validate_data):
          print('全局钩子')
          return validate_data
          
      # 校验字段的内置字段 validators=[check_data]
      def check_data(data):
      	print('validators校验')
      	return data
  ```

> 注意: 当出现`NotImplementedError: `update()` must be implemented.` 报错的时候
>
> 我们需要在序列化类中重写update方法
>
> ```Python
> def update(self, instance, validated_data):
>  # instance是student这个对象
>  # validated_data是校验后的数据
>  instance.id = validated_data.get('id')
>  instance.name = validated_data.get('name')
>  instance.age = validated_data.get('age')
>  instance.sex = validated_data.get('sex')
>  instance.description = validated_data.get('description')
>  instance.save()
>  return instance
> ```

+ 如果序列化和反序列化的字段不是一样的, 这里有两个字段参数
  + read_only	默认值为False
  + write_only    默认为False   相当于required
+ 查询所有数据
+ 删除数据

## 6. 模型类序列化器(ModelSerializer)

使用方法和serializers.Serializer()类似

不需要重写create和update

```Python
class StudentModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student  # 对应上models.py中的模型
        fields = '__all__'
        # fields = ('name','sex','age')  # 只序列化指定字段
        # exclude = ('id', 'sex',)  # 除了指定字段其他都序列化
        # read_only_fields = ('name',)  # 于序列化输出
        # write_only_fields = ('name',)  # 反序列化输入 该方法在2.x版本后弃用
        # 使用extra_kwargs来代替read_only_fields和write_only_fields
        extra_kwargs = {
            'name': {'min_length': 4, 'required': True, 'write_only': True},
        }
```

## 7. 源码分析

## 8. Serializer搭配source的高级使用

​	用于填充这个字段的属性名称。可以是一个仅接受`self`参数的方法，例如`URLField(source='get_absolute_url')`，或者使用点分符遍历属性，例如`EmailField(source='user.email')`。当使用点分符序列化字段时，如果在属性遍历期间不存在任何对象或该对象为空，则可能需要提供“默认”值。

​	该值`source='*'`具有特殊含义，用于指示应将整个对象传递给该字段。这对于创建嵌套表示或对于需要访问完整对象才能确定输出表示的字段很有用。

​	默认为字段名称。



# Day Three

## 1. 请求和响应

+ ### 请求对象

  ```python
  from rest-framework.request import Request
  def __init__(self, request, parsers=None, authenticators=None,
               negotiator=None, parser_context=None):
      assert isinstance(request, HttpRequest), (
          'The `request` argument must be an instance of '
          '`django.http.HttpRequest`, not `{}.{}`.'
          .format(request.__class__.__module__, request.__class__.__name__)
      )
  	# 二次封装request,将原生request作为drf request对象的_request 属性
      self._request = request
  def __getattr__(self,item):
      return getattr(self._request,item)
  ```

  + 请求对象.data
  + 请求对象.query_params

+ ### 响应对象

  ```Python
  #from rest_framework.response import Response def __init__(self, data=None, status=None,                 template_name=None, headers=None,                 exception=False, content_type=None):        #data：你要返回的数据，字典#status：返回的状态码，默认是200，	-from rest_framework import status在这个路径下，它把所有使用到的状态码都定义成了常量#template_name 渲染的模板名字（自定制模板），不需要了解#headers:响应头，可以往响应头放东西，就是一个字典#content_type：响应的编码格式，application/json和text/html;# 浏览器响应成浏览器的格式，postman响应成json格式，通过配置实现的（默认配置）#不管是postman还是浏览器，都返回json格式数据# drf有默认的配置文件---》先从项目的setting中找，找不到，采用默认的# drf的配置信息，先从自己类中找--》项目的setting中找---》默认的找	-局部使用:对某个视图类有效        -在视图类中写如下        from rest_framework.renderers import JSONRenderer        renderer_classes=[JSONRenderer,]    -全局使用：全局的视图类，所有请求，都有效    	-在setting.py中加入如下        REST_FRAMEWORK = {            'DEFAULT_RENDERER_CLASSES': (  # 默认响应渲染类                'rest_framework.renderers.JSONRenderer',  # json渲染器                'rest_framework.renderers.BrowsableAPIRenderer',  # 浏览API渲染器            )        }
  ```

## 2. 视图

```
两个视图基类:APIViewGenericAPIView
```

+ ###  基于APIView写接口

```python
#### views.pyfrom rest_framework.generics import GenericAPIViewfrom app01.models import Bookfrom app01.ser import BookSerializer# 基于APIView写的class BookView(APIView):    def get(self,request):        book_list=Book.objects.all()        book_ser=BookSerializer(book_list,many=True)        return Response(book_ser.data)    def post(self,request):        book_ser = BookSerializer(data=request.data)        if book_ser.is_valid():            book_ser.save()            return Response(book_ser.data)        else:            return Response({'status':101,'msg':'校验失败'})class BookDetailView(APIView):    def get(self, request,pk):        book = Book.objects.all().filter(pk=pk).first()        book_ser = BookSerializer(book)        return Response(book_ser.data)    def put(self, request,pk):        book = Book.objects.all().filter(pk=pk).first()        book_ser = BookSerializer(instance=book,data=request.data)        if book_ser.is_valid():            book_ser.save()            return Response(book_ser.data)        else:            return Response({'status': 101, 'msg': '校验失败'})    def delete(self,request,pk):        ret=Book.objects.filter(pk=pk).delete()        return Response({'status': 100, 'msg': '删除成功'})    #models.pyclass Book(models.Model):    name=models.CharField(max_length=32)    price=models.DecimalField(max_digits=5,decimal_places=2)    publish=models.CharField(max_length=32)#ser.pyclass BookSerializer(serializers.ModelSerializer):    class Meta:        model=Book        fields='__all__'# urls.pypath('books/', views.BookView.as_view()),re_path('books/(?P<pk>\d+)', views.BookDetailView.as_view()),
```

+ ### 基于GenericAPIView写的接口

```python
# views.pyclass Book2View(GenericAPIView):    #queryset要传queryset对象，查询了所有的图书    # serializer_class使用哪个序列化类来序列化这堆数据    queryset=Book.objects    # queryset=Book.objects.all()    serializer_class = BookSerializer    def get(self,request):        book_list=self.get_queryset()        book_ser=self.get_serializer(book_list,many=True)        return Response(book_ser.data)    def post(self,request):        book_ser = self.get_serializer(data=request.data)        if book_ser.is_valid():            book_ser.save()            return Response(book_ser.data)        else:            return Response({'status':101,'msg':'校验失败'})class Book2DetailView(GenericAPIView):    queryset = Book.objects    serializer_class = BookSerializer    def get(self, request,pk):        book = self.get_object()        book_ser = self.get_serializer(book)        return Response(book_ser.data)    def put(self, request,pk):        book = self.get_object()        book_ser = self.get_serializer(instance=book,data=request.data)        if book_ser.is_valid():            book_ser.save()            return Response(book_ser.data)        else:            return Response({'status': 101, 'msg': '校验失败'})    def delete(self,request,pk):        ret=self.get_object().delete()        return Response({'status': 100, 'msg': '删除成功'})     #url.py    # 使用GenericAPIView重写的    path('books2/', views.Book2View.as_view()),    re_path('books2/(?P<pk>\d+)', views.Book2DetailView.as_view()),
```

+ ### 基于GenericAPIView和5个视图扩展类写的接口

  + ListModelMixin
  + CreateModelMixin
  + UpdateModelMixin
  + DestroyModelMixin
  + RetrieveModelMixin

```python
from rest_framework.mixins import  ListModelMixin,CreateModelMixin,UpdateModelMixin,DestroyModelMixin,RetrieveModelMixin# views.pyclass Book3View(GenericAPIView,ListModelMixin,CreateModelMixin):    queryset=Book.objects    serializer_class = BookSerializer    def get(self,request):        return self.list(request)    def post(self,request):        return self.create(request)class Book3DetailView(GenericAPIView,RetrieveModelMixin,DestroyModelMixin,UpdateModelMixin):    queryset = Book.objects    serializer_class = BookSerializer    def get(self, request,pk):        return self.retrieve(request,pk)    def put(self, request,pk):        return self.update(request,pk)    def delete(self,request,pk):        return self.destroy(request,pk)# urls.py    # 使用GenericAPIView+5 个视图扩展类  重写的    path('books3/', views.Book3View.as_view()),    re_path('books3/(?P<pk>\d+)', views.Book3DetailView.as_view()),
```

+ ### 使用ModelViewSet编写5个接口

```python
# views.pyfrom rest_framework.viewsets import ModelViewSetclass Book5View(ModelViewSet):  #5个接口都有，但是路由有点问题    queryset = Book.objects    serializer_class = BookSerializer    # urls.py# 使用ModelViewSet编写5个接口    path('books5/', views.Book5View.as_view(actions={'get':'list','post':'create'})), #当路径匹配，又是get请求，会执行Book5View的list方法    re_path('books5/(?P<pk>\d+)', views.Book5View.as_view(actions={'get':'retrieve','put':'update','delete':'destroy'})),
```

+ ### 源码分析ViewSetMixin

```python
# views.pyfrom rest_framework.viewsets import ViewSetMixinclass Book6View(ViewSetMixin,APIView): #一定要放在APIVIew前    def get_all_book(self,request):        print("xxxx")        book_list = Book.objects.all()        book_ser = BookSerializer(book_list, many=True)        return Response(book_ser.data)    # urls.py    #继承ViewSetMixin的视图类，路由可以改写成这样    path('books6/', views.Book6View.as_view(actions={'get': 'get_all_book'})),
```

# Day Four

## 1. 路由

+ ### 1 在urls.py中配置

  ```Python
  path('books4/', views.Book4View.as_view()),re_path('books4/(?P<pk>\d+)', views.Book4DetailView.as_view()),
  ```

+ ### 2 一旦视图类，继承了ViewSetMixin，路由

  ```Python
  path('books5/', views.Book5View.as_view(actions={'get':'list','post':'create'})), #当路径匹配，又是get请求，会执行Book5View的list方法re_path('books5/(?P<pk>\d+)', views.Book5View.as_view(actions={'get':'retrieve','put':'update','delete':'destroy'})),
  ```

+ ### 3 继承自视图类，ModelViewSet的路由写法（自动生成路由）

  前提是序列化类必须要使用`ModelSerializer`

  ```Python
  -urls.py    # 第一步：导入routers模块    from rest_framework import routers    # 第二步：有两个类,实例化得到对象    # routers.DefaultRouter 生成的路由更多    # routers.SimpleRouter    router=routers.DefaultRouter()    # 第三步：注册    # router.register('前缀','继承自ModelViewSet视图类','别名')    router.register('books',views.BookViewSet) # 不要加斜杠了    # 第四步    # router.urls # 自动生成的路由,加入到原路由中    # print(router.urls)    # urlpatterns+=router.urls    -views.py    from rest_framework.viewsets import ModelViewSet    from app01.models import Book    from app01.ser import BookSerializer    class BookViewSet(ModelViewSet):        queryset =Book.objects        serializer_class = BookSerializer
  ```

> action的使用
>
> ```python
> # action干什么用？为了给继承自ModelViewSet的视图类中定义的函数也添加路由# 使用class BookViewSet(ModelViewSet): queryset =Book.objects.all() serializer_class = BookSerializer # methods第一个参数，传一个列表，列表中放请求方式， # ^books/get_1/$ [name='book-get-1'] 当向这个地址发送get请求，会执行下面的函数 # detail：布尔类型 如果是True #^books/(?P<pk>[^/.]+)/get_1/$ [name='book-get-1'] # 当detail=True的时候需要传入pk,如果设为False,就不需要传入pk @action(methods=['GET','POST'],detail=True) def get_1(self,request,pk):     print(pk)     book=self.get_queryset()[:2]  # 从0开始截取2条     ser=self.get_serializer(book,many=True)     return Response(ser.data)# 装饰器，放在被装饰的函数上方，method：请求方式，detail：是否带pk
> ```

## 2. 认证

+ ### 认证的实现

```Python
1. 写一个类,继承BaseAuthentication, 重写authticate, 认证的逻辑写在里面	认证通过,返回两个值,一个值最终传给了Request对象的user,认证失败,抛出异常APIException或者AuthenticationFailed2. 全局使用,局部使用
```

+ ### 认证源码分析

```Python
#1 APIVIew----》dispatch方法---》self.initial(request, *args, **kwargs)---->有认证，权限，频率#2 只读认证源码： self.perform_authentication(request)#3 self.perform_authentication(request)就一句话：request.user，需要去drf的Request对象中找user属性（方法） #4 Request类中的user方法，刚开始来，没有_user,走 self._authenticate()#5 核心，就是Request类的 _authenticate(self):    def _authenticate(self):        # 遍历拿到一个个认证器，进行认证        # self.authenticators配置的一堆认证类产生的认证类对象组成的 list        #self.authenticators 你在视图类中配置的一个个的认证类：authentication_classes=[认证类1，认证类2]，对象的列表        for authenticator in self.authenticators:            try:                # 认证器(对象)调用认证方法authenticate(认证类对象self, request请求对象)                # 返回值：登陆的用户与认证的信息组成的 tuple                # 该方法被try包裹，代表该方法会抛异常，抛异常就代表认证失败                user_auth_tuple = authenticator.authenticate(self) #注意这self是request对象            except exceptions.APIException:                self._not_authenticated()                raise            # 返回值的处理            if user_auth_tuple is not None:                self._authenticator = authenticator                # 如何有返回值，就将 登陆用户 与 登陆认证 分别保存到 request.user、request.auth                self.user, self.auth = user_auth_tuple                return        # 如果返回值user_auth_tuple为空，代表认证通过，但是没有 登陆用户 与 登陆认证信息，代表游客        self._not_authenticated()
```

+ 认证组件的使用

```python
# 写一个认证类 app_auth.pyfrom rest_framework.authentication import BaseAuthenticationfrom rest_framework.exceptions import AuthenticationFailedfrom app01.models import UserTokenclass MyAuthentication(BaseAuthentication):    def authenticate(self, request):        # 认证逻辑，如果认证通过，返回两个值        #如果认证失败，抛出AuthenticationFailed异常        token=request.GET.get('token')        if  token:            user_token=UserToken.objects.filter(token=token).first()            # 认证通过            if user_token:                return user_token.user,token            else:                raise AuthenticationFailed('认证失败')        else:            raise AuthenticationFailed('请求地址中需要携带token')# 可以有多个认证，从左到右依次执行# 全局使用，在setting.py中配置REST_FRAMEWORK={    "DEFAULT_AUTHENTICATION_CLASSES":["app01.app_auth.MyAuthentication",]}# 局部使用，在视图类上写authentication_classes=[MyAuthentication]# 局部禁用authentication_classes=[]
```

# Day Five

## 1. 权限

+ 权限验证源码分析

```python
# APIView---->dispatch---->initial--->self.check_permissions(request)(APIView的对象方法)    def check_permissions(self, request):        # 遍历权限对象列表得到一个个权限对象(权限器)，进行权限认证        for permission in self.get_permissions():            # 权限类一定有一个has_permission权限方法，用来做权限认证的            # 参数：权限对象self、请求对象request、视图类对象            # 返回值：有权限返回True，无权限返回False            if not permission.has_permission(request, self):                self.permission_denied(                    request, message=getattr(permission, 'message', None)                )
```

+ 权限的使用

```python
# 写一个类，继承BasePermission，重写has_permission，如果权限通过，就返回True，不通过就返回Falsefrom rest_framework.permissions import BasePermissionclass UserPermission(BasePermission):    def  has_permission(self, request, view):        # 不是超级用户，不能访问        # 由于认证已经过了，request内就有user对象了，当前登录用户        user=request.user  # 当前登录用户        # 如果该字段用了choice，通过get_字段名_display()就能取出choice后面的中文        print(user.get_user_type_display())        if user.user_type==1:            return True        else:            return False        # 局部使用class TestView(APIView):    permission_classes = [app_auth.UserPermission]# 全局使用REST_FRAMEWORK={    "DEFAULT_AUTHENTICATION_CLASSES":["app01.app_auth.MyAuthentication",],    'DEFAULT_PERMISSION_CLASSES': [        'app01.app_auth.UserPermission',    ],}# 局部禁用class TestView(APIView):    permission_classes = []
```

+ 内置权限

```python
# 演示一下内置权限的使用：IsAdminUser，控制是否对网站后台有权限的人# 1 创建超级管理员# 2 写一个测试视图类from rest_framework.permissions import IsAdminUserfrom rest_framework.authentication import SessionAuthenticationclass TestView3(APIView):    authentication_classes=[SessionAuthentication,]    permission_classes = [IsAdminUser]    def get(self,request,*args,**kwargs):        return Response('这是22222222测试数据，超级管理员可以看')# 3 超级用户登录到admin，再访问test3就有权限# 4 正常的话，普通管理员，没有权限看（判断的是is_staff字段）
```



## 2. 频率

+ 内置的频率限制(限制未登录用户)

```python
# 全局使用  限制未登录用户1分钟访问5次REST_FRAMEWORK = {    'DEFAULT_THROTTLE_CLASSES': (        'rest_framework.throttling.AnonRateThrottle',    ),    'DEFAULT_THROTTLE_RATES': {        'anon': '3/m',    }}##############views.pyfrom rest_framework.permissions import IsAdminUserfrom rest_framework.authentication import SessionAuthentication,BasicAuthenticationclass TestView4(APIView):    authentication_classes=[]    permission_classes = []    def get(self,request,*args,**kwargs):        return Response('我是未登录用户')# 局部使用from rest_framework.permissions import IsAdminUserfrom rest_framework.authentication import SessionAuthentication,BasicAuthenticationfrom rest_framework.throttling import AnonRateThrottleclass TestView5(APIView):    authentication_classes=[]    permission_classes = []    throttle_classes = [AnonRateThrottle]    def get(self,request,*args,**kwargs):        return Response('我是未登录用户，TestView5')
```

+ 内置频率限制(限制登录用户的访问频次)

```python
# 需求：未登录用户1分钟访问5次，登录用户一分钟访问10次全局：在setting中  'DEFAULT_THROTTLE_CLASSES': (        'rest_framework.throttling.AnonRateThrottle',        'rest_framework.throttling.UserRateThrottle'    ),    'DEFAULT_THROTTLE_RATES': {        'user': '10/m',        'anon': '5/m',    }         局部配置：	在视图类中配一个就行
```



## 3. 过滤

```python
#1 安装：pip3 install django-filter#2 注册，在app中注册#3 全局配，或者局部配 'DEFAULT_FILTER_BACKENDS': ('django_filters.rest_framework.DjangoFilterBackend',)#4 视图类class BookView(ListAPIView):    queryset = Book.objects.all()    serializer_class = BookSerializer    filter_fields = ('name',)  #配置可以按照哪个字段来过滤
```



## 4. 排序

```python
# 局部使用和全局使用# 局部使用from rest_framework.generics import ListAPIViewfrom rest_framework.filters import OrderingFilterfrom app01.models import Bookfrom app01.ser import BookSerializerclass Book2View(ListAPIView):    queryset = Book.objects.all()    serializer_class = BookSerializer    filter_backends = [OrderingFilter]    ordering_fields = ('id', 'price')    # urls.pypath('books2/', views.Book2View.as_view()),]# 使用：http://127.0.0.1:8000/books2/?ordering=-pricehttp://127.0.0.1:8000/books2/?ordering=pricehttp://127.0.0.1:8000/books2/?ordering=-id
```



## 5. 异常处理

```python
#统一接口返回# 自定义异常方法，替换掉全局# 写一个方法# 自定义异常处理的方法from rest_framework.views import exception_handlerfrom rest_framework.response import Responsefrom rest_framework import statusdef my_exception_handler(exc, context):    response=exception_handler(exc, context)    # 两种情况，一个是None，drf没有处理    #response对象，django处理了，但是处理的不符合咱们的要求    # print(type(exc))    if not response:        if isinstance(exc, ZeroDivisionError):            return Response(data={'status': 777, 'msg': "除以0的错误" + str(exc)}, status=status.HTTP_400_BAD_REQUEST)        return Response(data={'status':999,'msg':str(exc)},status=status.HTTP_400_BAD_REQUEST)    else:        # return response        return Response(data={'status':888,'msg':response.data.get('detail')},status=status.HTTP_400_BAD_REQUEST)    # 全局配置setting.py'EXCEPTION_HANDLER': 'app01.app_auth.my_exception_handler',
```

## 6. 封装Response对象(重要)

```python
# 以后都用自己封装的class APIResponse(Response):    def __init__(self,code=100,msg='成功',data=None,status=None,headers=None,**kwargs):        dic = {'code': code, 'msg': msg}        if  data:            dic = {'code': code, 'msg': msg,'data':data}        dic.update(kwargs)        super().__init__(data=dic, status=status,headers=headers)# 使用return APIResponse(data={"name":'lqz'},token='dsafsdfa',aa='dsafdsafasfdee')return APIResponse(data={"name":'lqz'})return APIResponse(code='101',msg='错误',data={"name":'lqz'},token='dsafsdfa',aa='dsafdsafasfdee',header={})
```
