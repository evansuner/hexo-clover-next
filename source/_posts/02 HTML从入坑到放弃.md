---
title: 02 HTML从入坑到放弃
date: 2020-08-10 02:17:43.0
updated: 2021-11-09 08:24:12.288
url: /?p=14
categories: 
- 前端
tags: 
- HTML
---

# HTML简介

# 注释

+ 单行注释

  ```html
  <!--单行注释-->
  ```

+ 多行注释

  ```html
  <!--
  
  多行注释1
  
  多行注释2
  
  -->
  ```

  

  由于HTML代码非常的杂乱无章, 所以我们习惯性的用注释来划定区域方便后续的查找

  ```html
  <!--导航条开始-->
  
  导航条所需代码块
  
  <!--导航条结束-->
  
  <!--左侧菜单栏开始-->
  
  左侧菜单栏所需代码块
  
  <!--左侧菜单栏结束-->
  ```

# HTML文档结构

```html
<html>
    <head>head内的标签是不可见的</head>
    <body>body内的标签 些什么浏览器渲染什么</body>
</html>
<!--
文件的后缀名其实是给用户看的,只不过对应不同的 文件后缀名有不同的软件来出来,并添加更多的功能
注意: HTML代码是没有格式的,可以全部写在一行
-->

```

# 标签的分类

+ 单标签
+ 双标签

## head内常用标签

+ title
+ style
+ script
+ link
+ meta

## body内常用标签

### 基本标签

+ h1~h6
+ b  加粗
+ em   斜体(i)
+ u  下划线
+ s   删除线
+ p
+ br
+ hr

块级标签: 独占一行

行内标签: 自身文本多大占多大

## 特殊符号

+ \&nbsp;   空格
+ \&gt;   &gt;
+ \&lt;    &lt;
+ \&amp;  &amp;

+ \&yen;   ￥
+ \&copy;  &copy;

+ \&reg;    &reg;

## 常用标签

+ div	块级元素
+ span 	行内元素

上述的两个标签是在构造页面初期最常使用的  页面的布局一般先用div和span占位后再去调整样式

> p标签虽然是块级标签但是它的内部只能嵌套行内标签不能签到块级标签
>
> 如果嵌套了块级元素	浏览器不会报错,但是会自动分开

## img标签

```HTML
<img src="" alt="" title="" height="" width="">
src="地址"
alt="当图片加载不出来的时候的描述"
title="图片的名字,当鼠标悬浮显示" height="" width="">
height="" width="" 只设置一个参数图片等比例缩放
```

## a标签

```HTML
<a href="" target=""></a>
href="链接地址" 还可以放其他的id值,进行跳转
target="_blank" 新窗口打开,默认为_self
```

> 标签既可以有自带的属性也可以使用自定义属性

## 列表标签

### 无序列表(常用)

```html
<ul type="">
    <li></li>
    <li></li>
</ul>
type="circle" # 默认为实心圆点,circle空心圆点,square方形
```

### 有序列表(了解)

```html
<ol type="1" start="5">
    <li></li>
    <li></li>
</ol>
```

### 标题列表(了解)

```html
<dl>
    <dt>标题1</dt>
    <dd>内容1</dd>
    <dt>标题2</dt>
    <dd>内容2</dd>
</dl>
可以做多级目录
```

## 表格标签

```html
<table border="1" cellpadding="5" cellspacing="5">
        <thead>
        <tr>
            <th>username</th>
            <th>password</th>
        </tr>
        </thead>
            <tbody>
            <tr>
                <td>zhangsan</td>
                <td>xxxxxx</td>
            </tr>
            <tr>
                <td>lisi</td>
                <td>xxxxxx</td>
            </tr>
            </tbody>
</table>
tr标签内可以添加colspan="2"水平方向占两行 rowspan="2" 竖直方向占两行	类似于合并单元格
```

## 表单标签(**\*\*\*)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>注册页面</h1>
<form action="">
    <p>
        <label for="d1">username:
            <input type="text" name="password" placeholder="用户名" id="d1">
        </label>
    </p>
    <p>
        <label for="d2">password:
            <input type="password" name="password" placeholder="密码" id="d2">
        </label>
    </p>
    <p>
        <label for="d3">birthday:
            <input type="date" id="d3">
        </label>
    </p>
    <p>
        <label for="">性别:
            <input type="radio" name="gender" checked="checked">男
            <input type="radio" name="gender">女
        </label>
    </p>
    <p>
        <label for="">爱好:
            <input type="checkbox" checked>读书
            <input type="checkbox" checked>运动
            <input type="checkbox">睡觉
        </label>
    </p>
    <p>
        <label for="">文件:
            <input type="file" multiple>
        </label>
    </p>
    <p>
        <label for="">自我介绍:
            <textarea name="" id="" cols="30" rows="10"></textarea>
        </label>
    </p>
    <p>
        <label for="">province:
            <select name="" id="">
                <option value="">请选择</option>
                <option value="" selected>上海</option>
                <option value="">北京</option>
                <option value="">深圳</option>
            </select>
        </label>
    </p>
    <p>
        <label for="">前女友:
            <select name="" id="" multiple>
                <option value=""></option>
                <option value="" selected>新垣结衣</option>
                <option value="">斯嘉丽</option>
                <option value="">明老师</option>
            </select>
        </label>
    </p>
    <p>
        <label for="">province1:
            <select name="" id="">
                <optgroup label="上海">
                    <option value="">浦东</option>
                    <option value="">黄浦</option>
                </optgroup>
                <optgroup label="北京">
                    <option value="">朝阳</option>
                    <option value="">沙河</option>
                </optgroup>
            </select>
        </label>
    </p>
    <p>
        <label for="d4">
            <input type="submit" value="注册" id="d4">
        </label>
        <label for="d5"><input type="button" id="d5" value="按钮"></label>
        <label for="d6"><input type="reset" id="d6" value="重置"></label>
    </p>
</form>
</body>
</html>

action="" 控制数据提交的后端路径	
1. 什么都不写默认在当前页面的所在的url提交数据
2. 写全路径 https://evansuner.xyz
3. 只写路径后缀 /index/ 会自动识别出当前服务端的ip和port拼接到前面

method="" 提交方式 post get
label中的for需要关联到input中的id值 嵌不嵌套无所谓,重要是关联
label和input都是行内标签,使用p标签可以使他们在一行内显示

input标签 就类似于前端的变形金刚	通过type属性变形
	text: 普通文本
	password: 密文
	date: 日期
	submit: 触发form表单提交数据的动作 	        
	button: 本身不具有任何功能 但他是最有用的 可以js绑定自定义功能
	reset: 重置填写的所有的内容
	radio: 单选框
		默认选中要添加checked="checked"
		当属性名和属性值相同的时候可以简写checked
	checkbox: 多选框
		默认选中可以添加 checked
	file: 上传文件 也可以添加多个文件 加入属性 multiple
select标签 默认是单选,可以添加multiple变成多选
textarea标签	写入大量文本

# 能够触发form表单提交数据的按钮有哪些
1. <input type="button" value="按钮">
2. <button>提交</button> 本身就有提交功能,不需要再绑定提交事件
如果想要提交文本 必须要添加enctype="multipart/form-data"
```