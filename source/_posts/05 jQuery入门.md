---
title: 05 jQuery入门
date: 2020-08-16 02:17:46.0
updated: 2021-11-09 08:21:53.87
url: /?p=17
categories: 
- 前端
tags: 
- jquery
---

# jQuery简介

jQuery内部封装了原生的js代码(还额外添加了很多的功能)

能够让你通过书写更少的代码 完成js操作

类似于python里面的模块 在前端模块不叫模块 叫做 “类库”

兼容多个浏览器	你在使用jQuery的时候就不需要考虑浏览器的兼容问题

jQuery宗旨:	write less do more

# jQuery学习内容

+ 选择器
+ 筛选器
+ 样式操作
+ 文本操作
+ 属性操作
+ 文档处理
+ 事件
+ 动画效果
+ 插件
+ each、data、Ajax(重点)

# 针对导入问题

```js
/*
1. 文件下载到本地	如何解决多个文本反复书写引入语句的代码
	可以借助于pycharm自动初始化代码功能 完成自动添加
	settings -> editor -> file and code template
	
2. 直接使用cdn

*/
```

# 查找标签

## 基本选择器

```js
// id选择器
$("#idName")
// class selector
$(".className")
// element selector
$("eleName")

// jQuery对象如何变成标签对象
$("#idName")[0]  // 等价于 document.getElementBYId("idName")
// 标签对象如何转变成jQuery对象
$(document.getElementById('idName'))
```

## 组合选择器/分组与嵌套

```js
// $("div.className")
// $("div#idName")

// 组合选择器
$('#d1,c1,p') // 并列+混用
$('div span') // 后代
$('div>span') // 儿子
$('div+span') // 毗邻
$('div~span') // 弟弟
```

## 基本筛选器

```js
$('ul li') // 后代所有li
$('ul li:first') // 第一个
$('ul li:last')	// 最后一个
$('ul li:eq(2)') // 相当于索引
$('ul li:evan') // 索引为偶数
$('ul li:odd')	// 索引为奇数
$('ul li:gt(2)') // 索引大于2
$('ul li:lt(2)') // 索引小于2

$('ul li:not("#d1")') // 不要带有id是d1的其他li
$('div:has('p')') // 找一个内部含有p的div,如果不含有子标签是不行的
```

## 属性选择器

```js
$("[username]") // 找属性名为username的标签
$("[username='egon']") //找属性名为username以及其属性名为egon的标签
```

## 表单筛选器

```js
$('input[type="text"]')  ==  $(':text')
$('input[type="password"]') == $(':password')
$(':file')
$(':radio')
$(':checkbox')
$(':submit')
$(':reset')
$(':button')

// 还包括表单对象属性
$(':enabled')
$(':disabled')
$(':checked') // 这里要注意的是 :checked会将checked和selected都拿到\
// 可以加一个限制条件 $('input:checked')
$(':selected') // 只拿到selected
```

## 筛选器方法

```js
$('#d1').next() // 同级标签的下面一个标签
$('#d1').nextAll() // 同级标签的下面所有标签
$('#d1').nextUntil('.c1') // 同级标签后面的标签直到标签.c1

$('#d1').prev()
$('#d1').prevAll()
$('#d1').prevUntil('#d2')

$('#d1').parent()
$('#d1').parent().parent() // 支持链式调用
$('#d1').parents()
$('#d1').parentsUntil('#d2')

$('#d1').children()
$('#d1').siblinggs() // 同级别上下所有兄弟

$('#d1 p')
// 等价
$('#d1').find('a')

$('#d1 p:first')	== $('#d1 p').first()
$('#d1 p:last')	== $('#d1 p').last()
$('#d1 p:not(".p1")')	== $('#d1 p').not('.p1')
```

# 操作标签

## 样式操作

```js
// 操作类
// js版本					jquery版本			
classList.add()				addClass()
classList.remove()			removeClass()
classList.contains()		hasClass()
classList.toggle()			toggleClass()
```

```js
// CSS操作
/*
<p>111</p>
<p>222</p>
一行代码将第一个p标签变成红色,第二个p标签变成绿色
*/
$('p').first().css('color','red').next().css('color','green');
// jquery的链式操作
// jQuery对象调用jQuery方法返回的还是当前jQuery对象 也就可以继续调用
```

```js
// 位置操作
offset()	// 当前标签左上角对应的浏览器的偏移量
position()	// 当前标签左上角对应的父标签的偏移量
scrollLeft()// 滚动条距离左边的距离
scrollTop()	// 滚动条距离顶部的距离
```

```js
// 尺寸
$('p').height()		// 文本高度
$('p').width()		// 文本宽度
$('p').innerHeight()	// 文本高度 + padding
$('p').innerWidth()		// 文本宽度 + padding
$('p').outerHeight()	// 文本高度 + padding + border
$('p').outerWidth()		// 文本宽度 + padding + border
```

## 文本操作

```js
// js版本					// jQuery操作
innerText				  text()
innerHTML				  html()
value					  val()
fileEle.file[0]			   $('$d1')[0].files[0]		// 文件
```

## 属性操作

```js
// js版本					// jQuery版本
setAttribute()				attr(name,value)
getAttribute()				attr(name)
removeAttribute()			removeAttr(name)

/*
在用变量存储对象的时候 js中推荐	xxxEle
在jQuery中存储对象的时候推荐	$xxxEle
*/
// 针对选择按钮 checkbox radio... 不要使用attr
// 判断这些有没有被选中
$('#d1').prop('checked') // false | true
$('#d1').prop('checked',true)	// 设置selected=selected
```

> 总结:
>
> 对于标签上有的能够看到的属性和自定义属性使用`attr`
>
> 对于返回布尔值比如 checked radio option是否被选中那么就用 `prop`

## 文本操作

```js
'// js								// jquery
createElement('p')						$('<p>')
let pEle = document.createElement('p') 	 let $pEle = $('<p>')
pEle.innerText = 'hello world'			$pEle.text('hello world')
divEle.appendChild(pEle)	  			$('#d1').append($pEle)
						    		  $pEle.appendTo($('#d1')) //内部尾部追加
						    		  $('#d1').preappend($pEle)
						    		  $pEle.preappendTo($('#d1')) //内部头部追加
									  $('#d1').after($pEle)
									  $pELe.insertAfter($('#d1')) //追加在同级标签后面
									  $('#d1').before($pEle)
									  $pELe.insertBefore($('#d1')) //追加在同级标签前面

									  $('#d1').remove() // 删除标签
									  $('#d1').empty() // 清空标签内部所有内容
```

# jQuery事件

```js
// 原生js
let btnEle = document.getElementById('d1');
btnEle.onclick = function(){}

// jquery
// 第一张方式
$('#d1').click(function(){})
// 第二种方式	功能更加强大	支持事件委托
$('#d1').on('click',function(){})
```

## 克隆事件

```js
$('#d1').on('click',function(){
    // console.log(this)	this指代的永远都是当前被操作的对象
    // $(this) 就是把原生的操作对象(标签) 转换成jQuery对象
    $(this).clone().insertAfter($('body')); // clone默认情况下只克隆HTML和CSS 不克隆绑定的事件, 当我们将clone()里添加上clone(true) 就可以克隆对应事件了
})
```

## 自定义模态框

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/css/bootstrap.min.css"
          integrity="undefined" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/js/bootstrap.min.js" integrity="undefined"
            crossorigin="anonymous"></script>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"
            integrity="sha256-/xUj+3OJU5yExlq6GSYGSHk7tPXikynS7ogEvDej/m4=" crossorigin="anonymous"></script>
  <style>
    .cover{
      position: fixed;
      top: 0;
      left: 0;
      bottom: 0;
      right: 0;
      background-color: rgba(60,63,65,.4);
      z-index: 99;
    }
    .model{
      position: fixed;
      height: 180px;
      width: 300px;
      background-color: white;
      top: 50%;
      left: 50%;
      margin-top: -90px;
      margin-left: -150px;
      z-index: 100;
    }
    .hide{
      display: none;
    }
  </style>
</head>
<body>
<div>我是最下面的</div>
<button id="d1">给我出来</button>
<div class="cover hide"></div>
<div class="model hide">
  <p style="margin-top: 20px">username: <input type="text"></p>
  <p>password: <input type="password"></p>
  <input type="button" value="确认" id="">
  <input type="button" value="取消" id="d2">
</div>
<script>
  let $coverEle = $('.cover')
  let $modelEle = $('.model');
  $('#d1').click(function () {
    $coverEle.removeClass('hide');
    $modelEle.removeClass('hide');
  });
  $('#d2').on('click',function(){
    $coverEle.addClass('hide');
    $modelEle.addClass('hide');
  })
</script>
</body>
</html>
```

## 左边菜单

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/css/bootstrap.min.css"
          integrity="undefined" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/js/bootstrap.min.js" integrity="undefined"
            crossorigin="anonymous"></script>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"
            integrity="sha256-/xUj+3OJU5yExlq6GSYGSHk7tPXikynS7ogEvDej/m4=" crossorigin="anonymous"></script>
  <style>
    .left{
      float: left;
      background-color: darkgray;
      width: 20%;
      height: 100%;
      position: fixed;
    }
    .title{
      font-size: 36px;
      color: white;
      text-align: center;
    }
    .items{
      border: 1px solid skyblue;
    }
    .hide{
      display: none;
    }
  </style>
</head>
<body>
<!--业务需求,点击一个菜单,其他两个隐藏-->
<div class="left">
  <div class="menu">
    <div class="title">菜单一
      <div class="items">001</div>
      <div class="items">002</div>
      <div class="items">003</div>
    </div>
    <div class="title">菜单二
      <div class="items">001</div>
      <div class="items">002</div>
      <div class="items">003</div>
    </div>
    <div class="title">菜单三
      <div class="items">001</div>
      <div class="items">002</div>
      <div class="items">003</div>
    </div>
  </div>
</div>
<script>
  $('.title').on('click',function(){
    // 先给所有的items添加hide
    // 然后将被点击的title下的items的hide移除
    $('.items').addClass('hide')
    $(this).children().removeClass('hide')
  })
</script>
</body>
</html>
```

## 返回顶部

监测窗口scroll

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/css/bootstrap.min.css"
          integrity="undefined" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/js/bootstrap.min.js" integrity="undefined"
            crossorigin="anonymous"></script>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"
            integrity="sha256-/xUj+3OJU5yExlq6GSYGSHk7tPXikynS7ogEvDej/m4=" crossorigin="anonymous"></script>
  <style>
    .hide{
      display: none;
    }
    #d1{
      position: fixed;
      width: 30px;
      height: 30px;
      background-color: orange;
      right: 30px;
      bottom: 30px;
    }
  </style>
</head>
<body>
<a href="" id="d1"></a>
<div style="height: 500px;background-color: red"></div>
<div style="height: 500px;background-color: greenyellow"></div>
<div style="height: 500px;background-color: blue"></div>
<a href="#d1" class="hide">回到顶部</a>
<script>
  $(window).scroll(function(){
    if($(window).scrollTop()>100){
      $('#d1').removeClass('hide');
    }else{
      $('#d1').addClass('hide');
    }
  })
</script>
</body>
</html>
```

## 自定义登陆校验

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/css/bootstrap.min.css"
          integrity="undefined" crossorigin="anonymous">
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/js/bootstrap.min.js" integrity="undefined"
            crossorigin="anonymous"></script>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"
            integrity="sha256-/xUj+3OJU5yExlq6GSYGSHk7tPXikynS7ogEvDej/m4=" crossorigin="anonymous"></script>

</head>
<body>
<!--在获取用户的用户名和密码的时候如果用户没有填写,给出提出信息-->
<p>username:
  <input type="text" id="username">
</p>

<p>password:
  <input type="password" id="password">
</p>
<span style="color: red;font-size: 12px;height: 13px;vertical-align: center" id="msg" disabled></span>

<button id="d1">提交</button>
<script>
  $('#d1').click(function(){
    // 获取用户输入的用户名和密码 做校验
    let $username = $('#username').val();
    let $password = $('#password').val();
    if (!$username || !$password){
      $('#msg').attr('disabled',false).text('用户名和密码不能为空!');
    }
  })
  // $('#username').focus(function(){
  //   $('#msg').attr('disabled',true).empty();
  // })
  // $('#password').focus(function(){
  //   $('#msg').attr('disabled',true).empty();
  // })
  $('input').focus(function(){
    $('#msg').attr('disabled',true).empty()
  })
</script>
</body>
</html>
```

## input框实时监控

```html
<script>
  $('#d1').on('input',function(){
    console.log(this.value)
  })
</script>
```

## hover的两种状态

```js
// hover有两种状态 一种是鼠标放上去触发一次事件,移开之后还可以触发一次事件
// 也就是说在hover()中我们可以定义一个或两个函数,对应其事件动作
$('#d1').hover(function(){
    alert("i'm here");
})
$('#d1').hover(
	function(){
        alert('come here');
    },
    function(){
        alert('go away');
    }
)
```

## 键盘按键按下事件

```js
$(window).keydown(function(){
    console.log(event.keyCode)
  })
```

## 阻止后续事件

```html
<form action="">
  <span id="d1" style="color: red"></span>
  <input type="submit" id="d2">
</form>
<script>
  $('#d2').click(function (e) {
    $('#d1').text('show me')
    // 阻止标签后续事件的执行
    // 第一种方式  在代码执行的最后添加上 return false // 推荐
    // return false
    // 第二种方式  在定义function的使用添加上形参e,然后使用e.preventDefault()
    e.preventDefault() // 读取标签默认的事件
  })
</script>
```

## 阻止事件冒泡

```html
<div id="d1">div
    <p id="d2">div>p
    <span id="d3">
        div>p>span
    </span>
    </p>
</div>
<script>
    $('#d1').click(function(){
        alert('div');
    })
    $('#d2').click(function(){
        alert('p');
    })
    $('#d3').click(function(e){
        alert('span');
        // 阻止事件冒泡的方式1 return false
        // return false
        // 阻止事件冒泡的方式2   
        // e.stopPropagation();
    })
</script>
```

## 事件委托

```html
<button>是兄弟,就来砍我!!!</button>
<script>
  // 给页面上的所有button绑定点击事件
  // $('button').click(function (){
  //   alert(123);
  // });
  // 事件委托
  $('body').on('click','button',function(){
    alert(124); // 将body内所有的点击事件委托给button按钮触发
  })
  // 在指定范围内 将事件委托给某一个标签 无论改标签是事先写好的还是说后面动态创建的,他们触发同一套代码
</script>
```

## 页面加载

```js
// 等待页面加载完成再执行代码
window.onload = function(){
	// js代码
}
// jquery 
// 第一种方式
$(document).ready(function(){
    // jquery 代码
})
// 第二种
$(function(){
    // jquery 代码
})
// 第三种
// 直接写在body的最下方
```

# 动画效果

```js
$('#d1').hide(5000)
$('#d1').show(5000)
$('#d1').slideUp(5000)
$('#d1').slideDown(5000)
$('#d1').fadeOut(5000)
$('#d1').fadeIn(5000)
$('#d1').fadeTo(5000,0.4) // 5000ms opacity 0.4
```

# 补充

## each的两种使用方式

有了each之后就不需要自己写for循环了

```js
// each	类似于for循环
// 第一种
$('div').each(function(index){console.log(index)}) // 一个参数表示索引
$('div').each(function(index,obj){console.log(index,obj)}) // 两个参数第一个索引第二个div标签
// 第二种
$.each([111,222,333],function(index,obj){index,obj}) // each中存放两个参数一个是array 另一个是函数, 结果会遍历数组的index和对应元素
```

## data()方法

能够让标签帮我们存储数据 并且我们肉眼看不见

```js
$('div').data('info','describe')

// 取值
$('div').first().data('info')  // describe
// 删除
$('div').first().removeData('info')
```