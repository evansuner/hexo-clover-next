---
title: 03 CSS从入坑到放弃
date: 2020-08-12 02:17:44.0
updated: 2021-11-09 08:23:45.202
url: /?p=15
categories: 
- 前端
tags: 
- css
---

# CSS

层叠样式表

```html
# 注释
/*单行注释*/
/*
多行注释1
多行注释2
*/

通常我们在写css样式的时候也会用注释来划定样式区域
```

# CSS选择器

## 基本选择器

```css
/* id选择器*/
#idname{}
/* 类选择器*/
.classname{}
/* 元素/标签选择器*/
span{}
/* 通用选择器*/
*{}
```

## 组合选择器

```css
/*后代选择器*/
div span{
    /*空格表示后代*/
}
/*儿子选择器*/
div>span{
    /*大于号表示儿子*/
}
/*毗邻选择器*/
div+span{
    /*同级别的下面第一个span标签*/
}
/*弟弟选择器*/
div~span{
    /*同级别,处于同一个父标签下,div后面的所有span都是div的弟弟*/
}
```

## 属性选择器

```css
/*
1. 含有某个属性
2. 含有某个属性并且有某个值
3. 含有某个属性并且有某个值的某个标签
*/
/*属性选择器是以中括号包括的都是属性选择器*/

[username]{
    color:"red"
    /*将所有包含属性username的标签颜色变为红色*/
}

[username="jason"]{
    color:"red",
    /*将属性名为username并且属性值为Jason的所有标签颜色变为红色*/
}
input[username="jason"]{
    color="red",
     /*将属性名为username并且属性值为Jason的input标签颜色变为红色*/
}
```

## 分组与嵌套选择器

```css
div,p,div{color:"red"}
```

## 伪类选择器

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        a:link{
            color:red;
        /*访问之前的状态*/
        }
        a:hover{
            color:aqua;
        /*悬浮状态*/
        }
        a:active{
            color: black;
        /*鼠标点击不松开 激活态*/
        }
        a:visited{
            color: darkgray;
        /*鼠标点击之后*/
        }
        input:focus{
            background-color: red;
        /*input框获取焦点*/
        }
    </style>
</head>
<body>
<a href="https://www.evansuner.xyz">小轩在不在?</a>
<input type="text">
</body>
</html>
```

## 伪元素选择器

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
      /*p:first-letter{*/
      /*  font-size: 48px;*/
      /*  color: orange;*/
      /*}*/
      p:before{
        content: "你说的对";
        color: red;
      /*使用伪元素产生的文本鼠标是无法选中的*/
      }
      p:after{
        content: "没有错";
        color: orange;
      }
    </style>
</head>
<body>
<p>装备差进任何本都是血赚</p>
</body>
</html>

ps: before和after通常是用来清除浮动带来的影响,解决父标签塌陷额问题
```

## 选择器的优先级

```html
1. 选择器相同 书写顺序不同
   就近原则: 谁距离标签近就生效谁的
2. 选择器不同
   行内选择器 > id选择器 > 类选择器 > 标签选择器
```

# css属性相关

## 设置长宽

只有块级标签才能设置长宽, 行内标签即使写了也不会生效

```html
<style>
p {
	background-color:"red",
    width:"200px",
    height:"100px"
}
</style>
```

## 字体属性

```css
p {
    font-family:"Microsoft Sans Serif","微软雅黑";
    font-size:24px;
    font-weight:"bolder|lighter"; /*bolder lighter 100~900 inherit*/
    color:"red" /*英文单词, #eeeeee(颜色标号), rgb(255,255,255),rgba(128,23,45,.9)*/
}
```

## 文字属性

```css
p {
    text-align:"center"; /*left right center justify*/
    text-decoration:"underline"; /*underline overline line-through*/    	text-decoration:none; /*主要用于给a标签去掉自带的下划线*/
    font-size:16px;
    text-indent:32px; /*首行缩进两字符*/
}
```

## 背景属性

```css
p {
    background-color:"red";
    background-image:url(); /*默认要全部铺满*/
    background-repeat: no-repeat;/*repeat-x repeat-y no-repeat*/
    backfground-position: center center; /*左边 上面*/
    background: red url() no-repeat center center; /*支持简写,位置没有关系*/
    background-attachment: fixed; /*背景固定*/
}
```

## 边框

```css
p {
    border-width:1px;
    border-style:solid;
    border-color:black;
    border-left:'';
    border-right:'';
    border-top:'';
    border-bottom:'';
    width:100px;
    height:100px;
    border-redis:50%; /*画圆 长宽一致即可*/
    border 1px solid black;
}
```

## display属性

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
  <style>
    #d1{
      display: none;
    /*  隐藏标签不展示到前端页面 并且原来占有的位置也不存在了*/
    }
    #d2{
      display: inline; /*将标签设置为行内标签的特点, 无法再设置长宽*/
    }
    /*#d2{*/
    /*  display: inline-block; !*让标签既可以在一行内使用又可以设置长宽*!*/
    /*}*/
    #d3,#d4{
      display: block; /*将行内元素设置成块级元素,可以设置长宽*/
    }
  </style>
</head>
<body>
<div id="d1"></div>
<div id="d2" style="height: 100px;width: 100px;background-color: green"></div>
<div id="d3" style="height: 100px;width: 100px;background-color: red"></div>
<span id="d4" style="height: 100px;width: 100px;background-color: green"></span>
<span id="d5" style="height: 100px;width: 100px;background-color: red"></span>
</body>
</html>
```

## 盒子模型

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
  <style>
    p{
      /*margin-left:0;*/
      /*margin-top:0;*/
      /*margin-right: 0;*/
      /*margin-bottom: 0;*/
      margin:0;
    }
  </style>
</head>
<body>
<!--
盒子模型
  以快递盒为例
    快递盒与快递盒之间的距离 margin(外边距)
    盒子的厚度 border(边框)
    盒子的物体到盒子的距离 padding(内边距)
    物体的大小哦 content(内容)
-->
<p style="border:1px solid red"></p>
</body>
</html>
```

## 浮动

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
  <style>
    #d1{
      height: 200px;
      width: 200px;
      background-color: red;
      float: left;
    }
    #d1,#d2:after{
      content: "";
      clear: both;
    }
    #d2{
      height: 200px;
      width: 200px;
      background-color: green;
      float: left;
    }
  </style>
</head>
<body>
<div id="d1"></div>
<div id="d2"></div>

</body>
</html>
```

## 解决浮动带来的影响

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
  <style>
    body{margin:0;}
    #d{
      border: 3px solid black;
    }
    #d1{
      height: 100px;
      width: 100px;
      background-color: red;
      float: left;
    }
    #d2{
      height: 100px;
      width: 100px;
      background-color: yellowgreen;
      float: left;
    }
    .clearfix:after {
        content:"";
        display: block;
        clear: both;
    }
  </style>
</head>
<body>
<!--
解决方式:
1. 自己添加一个div并设置高度
2. 利用给自己添加的div设置clear属性
3. 给塌陷的元素的父级标签添加一个class 并使用伪元素after设置clear属性
    所以这里有一个规范: 在写HTML代码之前我们需要先写好处理浮动带来的影响的css代码
    .clearfix:after{
        content:"";
        display:block;
        clear:both;
    }
    之后只要在需要消除浮动的元素标签上添加上class的属性值.clearfix即可
-->
<div id="d" class="clearfix">
  <div id="d1"></div>
  <div id="d2"></div>
<!--  <div id="d3"></div>-->
</div>
</body>
</html>
```

## 溢出属性

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
  <style>
    p {
      height: 100px;
      width: 100px;
      border:1px solid red;
      padding:3px;
      overflow: hidden; /*visible(默认) hidden scroll auto*/

    }
  </style>
</head>
<body>

  <p>我的名字是 我的名字是 我的名字是 我的名字是  我的名字是 我的名字是 我的名字是 我的名字是 我的名字是</p>
</body>
</html>
```

### 实际应用

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
  <style>
    body{
      margin:0;
      background-color: #4e4e4e;
    }
    #d1{
      height: 200px;
      width: 200px;
      border-radius: 50%;
      border:2px solid white;
      margin:0 auto;
      overflow: hidden;
    }
    #d1>img{
      width: 100%;
    }
  </style>
</head>
<body>
<div id="d1">
  <img src="https://th.bing.com/th/id/OIP.WD4kWMiKlbvp6xqjO3jW8QAAAA?w=163&h=180&c=7&o=5&dpr=1.25&pid=1.7" alt="">
</div>
</body>
</html>
```

## 定位

+ 静态

  所有的标签默认都是静态的(static), 无法改变位置

+ 相对定位

  相对于标签原来的位置做移动(relative)

+ 绝对定位(常用)

  相对于已经定位过得父标签做移动(absolute),如果没有父标签就以body作为参照

+ 固定定位(常用)

  相对于浏览器窗口固定在某个位置(fixed)

## 验证浮动和定位是否脱离文档流(是否保留原来的位置)

+ 不脱离文档流
  + 相对定位
+ 脱离文档流
  + 浮动
  + 绝对定位
  + 固定定位

## z-index模态框

eg: 百度登陆页面 其实是三层结构:

1. 最底层是正常部分(z=0) 最远的
2. 黑色的透明区(z=99) 中间层
3. 白色的注册区域(z=100) 距离用户眼睛最近

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
  <style>
    body{margin:0}
    .cover{
      position: fixed;
      left: 0;
      top: 0;
      right: 0;
      bottom: 0;
      background-color: rgba(0,0,0,.3);
      z-index: 99;
    }
    .model{
      background-color:white;
      height: 200px;
      width: 400px;
      position: fixed;
      left: 50%;
      top: 50%;
      margin-left: -200px;
      margin-top: -100px;
      padding:20px;
      z-index: 100;
    }
  </style>
</head>
<body>
  <div>这是最底层的页面内容</div>
  <div class="cover"></div>
  <div class="model">
    <h1>登录页面</h1>
    <p>username:<input type="text"></p>
    <p>password:<input type="password"></p>
    <button>登录</button>
  </div>
</body>
</html>
```

## 透明度(opacity)

rgba() 只能影响颜色

opacity能够影响颜色和字体

## 简单博客首页

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Blog</title>
  <link rel="stylesheet" href="./index.css">
</head>
<body>
<div id="left">
  <div class="avatar">
    <img src="https://th.bing.com/th/id/OIP.WD4kWMiKlbvp6xqjO3jW8QAAAA?w=177&h=180&c=7&o=5&dpr=1.25&pid=1.7" alt="">
  </div>
  <div class="blog-title">
    <p>evan's blog</p>
  </div>
  <div class="info">
    <p>coding makes me happy</p>
  </div>
  <div class="links">
    <ul>
      <li><a href="">关于我</a></li>
      <li><a href="">微博</a></li>
      <li><a href="">公众号</a></li>
    </ul>
  </div>
  <div class="tags">
    <ul>
      <li><a href="">#Python</a></li>
      <li><a href="">#Java</a></li>
      <li><a href="">#Golang</a></li>
    </ul>
  </div>
</div>
<div id="right">
  <div class="article">
    <div class="article-title clearfix">
      <span class="title">论开车速度的重要性</span>
      <span class="date">2021/6/7</span>
    </div>
    <div class="article-body"><p>evan从来不开车,不在乎速度</p></div>
    <div class="article-bottom">
      <span>#Python&nbsp;&nbsp;&nbsp;</span><span>#Golang</span>
    </div>
  </div>
  <div class="article">
    <div class="article-title clearfix">
      <span class="title">论开车速度的重要性</span>
      <span class="date">2021/6/7</span>
    </div>
    <div class="article-body"><p>evan从来不开车,不在乎速度</p></div>
    <div class="article-bottom">
      <span>#Python&nbsp;&nbsp;&nbsp;</span><span>#Golang</span>
    </div>
  </div>
  <div class="article">
    <div class="article-title clearfix">
      <span class="title">论开车速度的重要性</span>
      <span class="date">2021/6/7</span>
    </div>
    <div class="article-body"><p>evan从来不开车,不在乎速度</p></div>
    <div class="article-bottom">
      <span>#Python&nbsp;&nbsp;&nbsp;</span><span>#Golang</span>
    </div>
  </div>
  <div class="article">
    <div class="article-title clearfix">
      <span class="title">论开车速度的重要性</span>
      <span class="date">2021/6/7</span>
    </div>
    <div class="article-body"><p>evan从来不开车,不在乎速度</p></div>
    <div class="article-bottom">
      <span>#Python&nbsp;&nbsp;&nbsp;</span><span>#Golang</span>
    </div>
  </div>
  <div class="article">
    <div class="article-title clearfix">
      <span class="title">论开车速度的重要性</span>
      <span class="date">2021/6/7</span>
    </div>
    <div class="article-body"><p>evan从来不开车,不在乎速度</p></div>
    <div class="article-bottom">
      <span>#Python&nbsp;&nbsp;&nbsp;</span><span>#Golang</span>
    </div>
  </div>
  <div class="article">
    <div class="article-title clearfix">
      <span class="title">论开车速度的重要性</span>
      <span class="date">2021/6/7</span>
    </div>
    <div class="article-body"><p>evan从来不开车,不在乎速度</p></div>
    <div class="article-bottom">
      <span>#Python&nbsp;&nbsp;&nbsp;</span><span>#Golang</span>
    </div>
  </div>
  <div class="article">
    <div class="article-title clearfix">
      <span class="title">论开车速度的重要性</span>
      <span class="date">2021/6/7</span>
    </div>
    <div class="article-body"><p>evan从来不开车,不在乎速度</p></div>
    <div class="article-bottom">
      <span>#Python&nbsp;&nbsp;&nbsp;</span><span>#Golang</span>
    </div>
  </div>
  <div class="article">
    <div class="article-title clearfix">
      <span class="title">论开车速度的重要性</span>
      <span class="date">2021/6/7</span>
    </div>
    <div class="article-body"><p>evan从来不开车,不在乎速度</p></div>
    <div class="article-bottom">
      <span>#Python&nbsp;&nbsp;&nbsp;</span><span>#Golang</span>
    </div>
  </div>
</div>
</body>
</html>
```

```css
/*这是blog首页的css样式*/
/*通用样式*/
body{
    margin: 0;
    background-color: #eee;
}
a{
    text-decoration: none;
}
ul{
    list-style-type: none;
    padding:0;
}
.clearfix:after{
    content:'';
    display: block;
    clear: both;
}
/*左侧样式*/
#left{
    float: left;
    width: 20%;
    height: 100%;
    position: fixed;
    background-color: #4e4e4e;
}
.avatar{
    height: 200px;
    width: 200px;
    border-radius: 50%;
    border:1px solid white;
    margin: 20px auto;
    overflow: hidden;
}
.avatar>img{
    width: 100%;
}
.blog-title,.info{
    color: darkgray;
    margin-top: 20px;
    text-align: center;
}
.links,.tags{
    margin-top: 80px;
    font-size: 24px;
    text-align: center;
}
.links a,.tags a{
    color:darkgray;
}
.tags li>a:hover,.links li>a:hover{
    color: white;
}
/*右侧样式*/
#right{
    float: right;
    width: 80%;
    height: 1000px;
}
.article{
    background-color: white;
    margin: 20px 40px 30px;
    box-shadow: 5px 5px 5px rgba(0,0,0,.4);
}
.title{
    font-size: 36px;
    float: left;
}
.date{
    float:left;
    margin: 20px 20px;
}
.article-title{
    border-left: 8px solid red;
    text-indent: 1em;
}
.article-body{
    font-size: 18px;
    text-indent: 2em;
    border-bottom: 1px solid #4e4e4e;
}
.article-bottom{
    padding: 10px 30px;
}

```