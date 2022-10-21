---
title: 06 books系列表接口概览
date: 2021-03-17 03:17:16.0
updated: 2021-11-09 08:18:35.235
url: /?p=12
categories: 
- 后端
tags: 
- Python
- django
---

## 1. books系列表接口

```python
# urls.py
from django.urls import path,re_path
from api import views
urlpatterns = [
    path('books/', views.BookAPIView.as_view()),
    re_path('books/(?P<pk>\d+)', views.BookAPIView.as_view()),
]
```

```python
# views.py
from rest_framework.response import Response

from api import models
from  rest_framework.views import APIView
from rest_framework.generics import GenericAPIView
from api.ser import BookModelSerializer

class BookAPIView(APIView):
    def get(self,request,*args,**kwargs):
        #查询单个和查询所有，合到一起
        # 查所有
        book_list=models.Book.objects.all().filter(is_delete=False)
        book_list_ser=BookModelSerializer(book_list,many=True)
        return Response(data=book_list_ser.data)
        #查一个

    def post(self,request,*args,**kwargs):
        # 具备增单条，和增多条的功能
        if isinstance(request.data,dict):

            book_ser=BookModelSerializer(data=request.data)
            book_ser.is_valid(raise_exception=True)
            book_ser.save()
            return Response(data=book_ser.data)
        elif isinstance(request.data,list):
            #现在book_ser是ListSerializer对象
            from rest_framework.serializers import ListSerializer
            book_ser = BookModelSerializer(data=request.data,many=True)  #增多条
            print('--------',type(book_ser))
            book_ser.is_valid(raise_exception=True)
            book_ser.save()
            # 新增---》ListSerializer--》create方法
            # def create(self, validated_data):
            #   self.child是BookModelSerializer对象
            #   print(type(self.child))
            #     return [
            #         self.child.create(attrs) for attrs in validated_data
            #     ]
            return Response(data=book_ser.data)

    def put(self,request,*args,**kwargs):
        # 改一个，改多个
        #改一个个
        if kwargs.get('pk',None):
            book=models.Book.objects.filter(pk=kwargs.get('pk')).first()
            book_ser = BookModelSerializer(instance=book,data=request.data,partial=True)  # 增多条
            book_ser.is_valid(raise_exception=True)
            book_ser.save()
            return Response(data=book_ser.data)
        else:
            #改多个,
            # 前端传递数据格式[{id:1,name:xx,price:xx},{id:1,name:xx,price:xx}]
            # 处理传入的数据  对象列表[book1，book2]  修改的数据列表[{name:xx,price:xx},{name:xx,price:xx}]
            book_list=[]
            modify_data=[]
            for item in request.data:
                #{id:1,name:xx,price:xx}

                pk=item.pop('id')
                book=models.Book.objects.get(pk=pk)
                book_list.append(book)
                modify_data.append(item)
            # 第一种方案，for循环一个一个修改
            #把这个实现
            # for i,si_data in enumerate(modify_data):
            #     book_ser = BookModelSerializer(instance=book_list[i], data=si_data)
            #     book_ser.is_valid(raise_exception=True)
            #     book_ser.save()
            # return Response(data='成功')
            # 第二种方案，重写ListSerializer的update方法
            book_ser = BookModelSerializer(instance=book_list,data=modify_data,many=True)
            book_ser.is_valid(raise_exception=True)
            book_ser.save()  #ListSerializer的update方法,自己写的update方法
            return Response(book_ser.data)
            # request.data
            #
            # book_ser=BookModelSerializer(data=request.data)

    def delete(self,request,*args,**kwargs):
        #单个删除和批量删除
        pk=kwargs.get('pk')
        pks=[]
        if pk:
            # 单条删除
            pks.append(pk)
        #不管单条删除还是多条删除，都用多条删除
        #多条删除
        # {'pks':[1,2,3]}
        else:
            pks=request.data.get('pks')
        #把is_delete设置成true
        # ret返回受影响的行数
        ret=models.Book.objects.filter(pk__in=pks,is_delete=False).update(is_delete=True)
        if ret:
            return Response(data={'msg':'删除成功'})
        else:
            return Response(data={'msg': '没有要删除的数据'})

```

```python
ser.py


from rest_framework import serializers
from api import models


#写一个类，继ListSerializer,重写update
class BookListSerializer(serializers.ListSerializer):
    # def create(self, validated_data):
    #     print(validated_data)
    #     return super().create(validated_data)
    def update(self, instance, validated_data):
        print(instance)
        print(validated_data)
        # 保存数据
        # self.child:是BookModelSerializer对象
        # ll=[]
        # for i,si_data in enumerate(validated_data):
        #     ret=self.child.update(instance[i],si_data)
        #     ll.append(ret)
        # return ll
        return [
            # self.child.update(对象，字典) for attrs in validated_data
            self.child.update(instance[i],attrs) for i,attrs in enumerate(validated_data)
        ]



#如果序列化的是数据库的表，尽量用ModelSerializer
class BookModelSerializer(serializers.ModelSerializer):
    # 一种方案（只序列化可以，反序列化有问题）
    # publish=serializers.CharField(source='publish.name')
    # 第二种方案，models中写方法

    class Meta:
        list_serializer_class=BookListSerializer
        model=models.Book
        # fields='__all__'
        # 用的少
        # depth=0
        fields = ('name','price','authors','publish','publish_name','author_list')

        extra_kwargs={
            'publish':{'write_only':True},
            'publish_name':{'read_only':True},
            'authors':{'write_only':True},
            'author_list':{'read_only':True}
        }
```

```python
# models.py
from django.db import models




from django.contrib.auth.models import AbstractUser
class BaseModel(models.Model):
    is_delete=models.BooleanField(default=False)
    # auto_now_add=True 只要记录创建，不需要手动插入时间，自动把当前时间插入
    create_time=models.DateTimeField(auto_now_add=True)
    # auto_now=True,只要更新，就会把当前时间插入
    last_update_time=models.DateTimeField(auto_now=True)
    # import datetime
    # create_time=models.DateTimeField(default=datetime.datetime.now)
    class Meta:
        # 单个字段，有索引，有唯一
        # 多个字段，有联合索引，联合唯一
        abstract=True  # 抽象表，不再数据库建立出表




class Book(BaseModel):
    id=models.AutoField(primary_key=True)
    # verbose_name admin中显示中文
    name=models.CharField(max_length=32,verbose_name='书名',help_text='这里填书名')
    price=models.DecimalField(max_digits=5,decimal_places=2)
    # 一对多的关系一旦确立，关联字段写在多的一方
    #to_field 默认不写，关联到Publish主键
    #db_constraint=False  逻辑上的关联，实质上没有外键练习，增删不会受外键影响，但是orm查询不影响
    publish=models.ForeignKey(to='Publish',on_delete=models.DO_NOTHING,db_constraint=False)

    # 多对多，跟作者，关联字段写在 查询次数多的一方

    # 什么时候用自动，什么时候用手动？第三张表只有关联字段，用自动    第三张表有扩展字段，需要手动写
    # 不能写on_delete
    authors=models.ManyToManyField(to='Author',db_constraint=False)
    class Meta:
        verbose_name_plural='书表'  # admin中表名的显示

    def __str__(self):
        return self.name

    @property
    def publish_name(self):
        return self.publish.name
    # def author_list(self):
    def author_list(self):
        author_list=self.authors.all()
        # ll=[]
        # for author in author_list:
        #     ll.append({'name':author.name,'sex':author.get_sex_display()})
        # return ll
        return [ {'name':author.name,'sex':author.get_sex_display()}for author in author_list]

class Publish(BaseModel):
    name = models.CharField(max_length=32)
    addr=models.CharField(max_length=32)
    def __str__(self):
        return self.name


class Author(BaseModel):
    name=models.CharField(max_length=32)
    sex=models.IntegerField(choices=((1,'男'),(2,'女')))
    # 一对一关系，写在查询频率高的一方
    #OneToOneField本质就是ForeignKey+unique，自己手写也可以
    authordetail=models.OneToOneField(to='AuthorDetail',db_constraint=False,on_delete=models.CASCADE)

class AuthorDetail(BaseModel):
    mobile=models.CharField(max_length=11)

# 二、表断关联
# 1、表之间没有外键关联，但是有外键逻辑关联(有充当外键的字段)
# 2、断关联后不会影响数据库查询效率，但是会极大提高数据库增删改效率（不影响增删改查操作）
# 3、断关联一定要通过逻辑保证表之间数据的安全，不要出现脏数据，代码控制
# 4、断关联
# 5、级联关系
#       作者没了，详情也没：on_delete=models.CASCADE
#       出版社没了，书还是那个出版社出版：on_delete=models.DO_NOTHING
#       部门没了，员工没有部门(空不能)：null=True, on_delete=models.SET_NULL
#       部门没了，员工进入默认部门(默认值)：default=0, on_delete=models.SET_DEFAULT
```

## 2. 分页器

```python
#views.py

# 查所有，才需要分页
from rest_framework.generics import ListAPIView
# 内置三种分页方式
from  rest_framework.pagination import PageNumberPagination,LimitOffsetPagination,CursorPagination
'''
PageNumberPagination
    page_size:每页显示的条数
'''
class MyPageNumberPagination(PageNumberPagination):
    #http://127.0.0.1:8000/api/books2/?aaa=1&size=6
    page_size=3  #每页条数
    page_query_param='aaa' #查询第几页的key
    page_size_query_param='size' # 每一页显示的条数
    max_page_size=5    # 每页最大显示条数


# class MyLimitOffsetPagination(LimitOffsetPagination):
#     default_limit = 3   # 每页条数
#     limit_query_param = 'limit' # 往后拿几条
#     offset_query_param = 'offset' # 标杆
#     max_limit = 5   # 每页最大几条

class MyCursorPagination(CursorPagination):
    cursor_query_param = 'cursor'  # 每一页查询的key
    page_size = 2   #每页显示的条数
    ordering = '-id'  #排序字段
# class BookView(ListAPIView):
#     # queryset = models.Book.objects.all().filter(is_delete=False)
#     queryset = models.Book.objects.all()
#     serializer_class = BookModelSerializer
#     #配置分页
#     pagination_class = MyCursorPagination

# 如果使用APIView分页
from utils.throttling import MyThrottle
class BookView(APIView):
    # throttle_classes = [MyThrottle,]
    def get(self,request,*args,**kwargs):
        book_list=models.Book.objects.all()
        # 实例化得到一个分页器对象
        page_cursor=MyPageNumberPagination()

        book_list=page_cursor.paginate_queryset(book_list,request,view=self)
        next_url =page_cursor.get_next_link()
        pr_url=page_cursor.get_previous_link()
        # print(next_url)
        # print(pr_url)
        book_ser=BookModelSerializer(book_list,many=True)
        return Response(data=book_ser.data)

    
  

#settings.py
REST_FRAMEWORK={
    'PAGE_SIZE': 2,
}
```

## 3. 根据ip进行频率限制

```python
# 写一个类，继承SimpleRateThrottle，只需要重写get_cache_key 
from rest_framework.throttling import ScopedRateThrottle,SimpleRateThrottle

#继承SimpleRateThrottle
class MyThrottle(SimpleRateThrottle):
    scope='luffy'
    def get_cache_key(self, request, view):
        print(request.META.get('REMOTE_ADDR'))
        return request.META.get('REMOTE_ADDR')   # 返回
    
# 局部使用，全局使用 
REST_FRAMEWORK={
    'DEFAULT_THROTTLE_CLASSES': (
        'utils.throttling.MyThrottle',
    ),
    'DEFAULT_THROTTLE_RATES': {
        'luffy': '3/m'  # key要跟类中的scop对应
    },
}

# python3 manage.py runserver 0.0.0.0:8000   你们局域网就可以相互访问


# 内网穿透
```

