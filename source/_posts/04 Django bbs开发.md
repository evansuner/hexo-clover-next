---
title: 04 Django bbs开发
date: 2021-03-12 03:17:13.0
updated: 2021-11-09 03:55:57.215
url: /?p=10
categories: 
- 后端
tags: 
- Python
- django
---

## 项目开发流程

1. 需求分析

   架构师 + 产品经理 + 开发者组长

   + 在跟客户谈需求之前, 会大致先了解客户的需求, 然后自己设计一套比较好写的方案

   + 再跟客户沟通交流中引导客户往我们之前想好的方案上靠

   + 形成一个初步方案

2. 项目设计

   架构师

   + 编程语言的选择

   + 框架选择

   + 数据库选择

     + 主库: MySQL, postgreSQL

     + 缓存数据: redis, MongoDB,memcache ....

   + 功能划分

   ​		将整个项目划分成几个功能模块

   + 找组长开会

   ​		分发任务

   + 项目报价

     + 技术这块需要多少人, 多少天

     + 产品经理公司层面  再加点钱

     ​			公司财务签字确认

     ​			公司老板签字确认

     + 产品经理去和客户沟通

     + 后续加功能	继续加钱

3. 分组开发

   组长找组员开会, 安排各自的功能模块

   我们其实就是在架构师设计好的框架内填写自己的代码(码畜)

   我们在写代码的时候胡 写完需要自己先测试是否有bug

   如果一些显而易见的bug,你没有避免而是直接交给测试部门测出来,可能就需要扣绩效

4. 测试

   测试部门测试

   + 压力测试
   + ...

5. 交付上线

   + 交给对方的韵味成员
   + 直接上线自己的服务器上

## 表设计

```Python
# 一个项目中最重要的不是业务逻辑的书写,而是前期的表设计,只有将表设计好了,后续的功能书写才会一帆风顺
bbs表设计:
    1. 用户表
    	继承AbstractUser
        拓展:
            phone
            avatar
            create_time
        外键字段:
            一对一 个人站点表
    2. 个人站点表
    	site_name	站点名称
    	site_title	站点标题
    	site_theme	站点样式      
    3. 文章标签表
    	name	标签名
        外键字段:
            一对多 个人站点表
    4. 文章分类表
    	name	分类名
        外键字段:
            一对一 个人站点表
    5. 文章表
    	title	文章标题
        desc	文章简介
        content	文章内容
        create_time	发布时间
        
        数据库字段设计优化(虽然下述可以从其他表里面跨表查询得到,但是频繁跨表会导致效率降低)
        up_num		点赞数
        down_num	点踩数
        comment_num	评论数
        
        外键字段:
            一对多 个人站点表
            多对多	文章标签表
            一对多	文章分类表
        
    6. 点赞点踩表
    	记录哪个用户给哪篇文章点了赞还是点了踩
        user	ForeignKey(to='User')
        article	ForeignKey(to='Article')
        is_up	BooleanField()
            
    7. 文章评论表
    	记录哪个用户给哪篇文章写了哪些内容
        user	ForeignKey(to='User')
        article	ForeignKey(to='Article')
        content	CharField()
        comment_time DataField()
        # 自关联
        parent	ForeignKey(to='Comment',null=True)
        # orm专门提供的自锻炼写法
        parent	ForeignKey(to='self',null=True)
根评论自评论的概念
	根评论就是直接评论当前发布的内容
    1. PHP是世界上最牛的语言
    	1.1 Python才是最牛逼的
        	1.2 Java才是
	根评论与子评论是一对多的关系
```

## 注册功能

```Python
我们之前是直接在views中书写的forms组件代码
但是我们为了解耦和 应该将所有的forms组件代码单独写到一个地方

如果你的项目自始至终只用到一个forms组件,那么你可以直接创建一个py文件书写即可
如果你的项目需要使用到多个forms组件,你需要创建一个文件夹,在文件夹内根据不同的forms组件功能创建不同的py文件
	myforms文件夹
    	regforms.py
        logforms.py
        userforms.py
        orderforms.py
        
```