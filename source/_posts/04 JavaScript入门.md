---
title: 04 JavaScript入门
date: 2020-08-22 02:17:46.0
updated: 2021-11-09 08:21:17.9
url: /?p=18
categories: 
- 前端
tags: 
- JavaScript 
---

|                           |          |                            |
| ------------------------- | -------- | -------------------------- |
| .length                   | len()    | 返回长度                   |
| .trim()                   | strip()  | 移除空白                   |
| .trimLeft()               | lstrip() | 移除左边空白               |
| .trimRight()              | rstrip() | 移除有边空白               |
| .charAt(n)                |          | 返回第n个字符              |
| .concat(value,...)        | join()   | 拼接                       |
| .indexOf(substring,start) |          | 子序列的位置               |
| .substring(from,to)       | [:]      | 根据索引获取子序列(不使用) |
| .slice(start,end)         |          | 切片(以后全部使用slice)    |
| .toLowerCase()            | lower()  | 小写                       |
| toUpperCase()             | upper()  | 大写                       |
| .split(delimiter,limit)   | split()  | 分割                       |

## 布尔值(boolean)

```js
// 1. 在python中bool值首字母大写
// 		True, False
// 2. 但是在js中bool值是全小写的
//		true,false
/*
布尔值是false的值有哪些?
python:
	0,空字符串,空字典,空列表
js:
	空字符串,0,null,undefined,NaN
*/
```

### null与undefined

```js
/*
null
	表示值为空 一般都是指定或者清空一个变量的时候使用
	name = "jason"
	name = null
undefined 
	表示声明了一个变量 但是没有给他做初始化操作(没有赋值)
*/
```

## 对象(object)

一切皆对象

+ 数组(类似于python的数组)

  不支持负数索引

+ 自定义对象(类似于python字典)

### 数组方法

![](C:\Users\evans\Documents\Typora\前端\front-end-learning\04 JavaScript.assets\JS数组方法.png)

## 运算符

``` js
// 算术运算符
let x = 10;
let res1 = x++;
let res2 = ++x;
// res1 = 10 res2=12
//加号在后面 先赋值后自增1
// 加号在前面 先自增后赋值

// 比较运算符
1 == "1" //返回 true, js内部自动换成相同的数据类型 == 弱等于
1 === "1" // 返回 false ===强等于 js内部不会做数据类型转换
1 != "1" // false
1 !== "1" // true

// 逻辑运算符
// python 	and or not
// js中 && || !
5 && '5' // '5'
0 || 1 // 1
!5 && '5' // 5
// 这里要注意的是比较运算返回的是布尔值, 逻辑运算符返回的是数据

// 赋值运算符
= += -= *= ....
```

## 流程控制

```js
// if判断
let age = 28;
if(condition){条件成立之后的代码块}
// id-else
if(condition){条件成立代码块}else{条件不成立代码块}
// if-else if else
if(codition1){}else if(condition2){}else{}
```

```js
// switch语法
/*
提前举例号可能出现的调节和解决方式
break的作用: 没有break的话,当匹配到一个之后就一直往下执行
default的作用: 如果都没有匹配到的话,就默认执行其下代码
*/
let num = 2;
switch(num){
    case 0:
        console.log('drink');
        break;
    case 1:
        console.log('sing');
        break;
    case 2:
        console.log('eat');
        break;
    case 3:
        console.log('bye bye');
        break;
    default:
        console.log('default value');
}
```

```js
// for循环
// 打印0~9数字
for(let i=0;i<10;i++){
    console.log(i);
}
// question print all element from arr
let arr = [111,222,333,444,555,666]
for (let i = 0; i<arr.length;i++){
    console.log(arr[i]);
}
```

```js
// while循环
while(i<100){
    console.log(i);
    i++;
}
```

```js
// 三元运算符
// python中的三元运算 res = 1 if 1>2 else 3
// js中的三元运算
let res = 1 > 2?1:3
let res = 2>5?8:(8>5?666:444)
// 条件成立取冒号前面的值不成立取冒号后面的值
// 三元运算符不要写的过于复杂
```

## 函数

```js
// 在python中定义函数使用def
// 在js中定义函数使用function
// function 函数名(形参1,形参2,...){函数体}
// 无参函数
function func1(){
    console.log('hello world');
}

// 有参函数
function func2(a,b){
    console.log(a,b);
}
// js函数在传参的时候不论传多还是传少都不会报错
// func2(1) 返回 1,undefined 
// func2(1,2,3,4) 返回 1,2

// 关键字arguments
function func2(a,b){
    console.log(arguments);
    console.log(a,b);
}
// arguments能够获取到函数接收到是的所有参数
function func2(a,b){
    if(arguments.length<2){
        console.log('参数传少了');
    }else if(arguments.length>2){
        console.log('参数传多了');
    }else{
        console.log(a,b);
}
    
// 函数的返回值
// 使用的也是关键字return
function index(){
    return 666,777,888;
}
res = index() 
// res的返回值只能是最后一个值888,要想返回所有有的值,必须手动将值封装成元组
// 不同于python,python在返回多个值的时候,在调用的时候会自动封装一个元组
    

// 匿名函数
function(){
    console.log('hahaha');
}
let res = function(){
    console.log('hahaha');
}


// 箭头函数
let func1 = v => v; // 箭头左边是形参 右边是返回值
/*
等价于
var func1 = function(v){
	return v;
}

箭头函数不是让我们来写复杂的函数的,他相当于python的匿名函数
*/
let func2 = (arg1,arg2) => arg1+arg2
//等价于
let func2 = function(arg1,arg2){
    return arg1+arg2;
}
 
```

## 函数的全局变量和局部变量

和python的变量查找方向一样

```js
let city="beijing";
function f(){
    let city="shanghai";
    function inner(){
        let city="shenzhen";
        console.log(city);
    }
    inner();
}
f();
// "shenzhen"
```

```js
let city = "beijing";
function bar(){
    console.log(city);
}
function f(){
    let city = "shanghai";
    return bar;
}
let ret = f();
ret();
// "beijing"
```

```js
let city = "beijing";
function f(){
    let city = "shanghai";
    function inner(){
        console.log(city);
    }
    return inner;
}
let ret = f();
ret();
// "shanghai"
```

## 自定义对象

可以看成python中的字典 但是js中的自定义对象比python里面的字典操作更加简单

+ 第一种创建自定义对象的方式

```js
// 创建自定义对象 {}
let d1 = {"name":"zhangsan","age":18};
typeOf d1 // 返回object
// 取值 d1["name"]
// 取值 d1.name	python不具备,但是js没有get方法
// for循环取值
for(let i in d1){
    console.log(i,d1[i])
}
```

+ 第二种创建方式

  使用关键 `new`  创建之后是一个空对象

```js
let d2 = new Object();
d2.name = 'zhangsan';
d2.age = 18;

```

## Date对象

```js
let d3 = new Date()
d3.toLocaleString()
d3.ToLocaleTimeString()

let d4 = new Date("2021/11/11 11:11:11")
d4.toLocaleString()
```

## JSON对象

```js
/*
在python中序列化反序列化
dumps 序列化
loads 反序列化
*/
// js中也有序列化和反序列化
JSON.stringify()	dumps
JSON.parse()	loads
```

| python     | json   | js     |
| ---------- | ------ | ------ |
| dict       | object | object |
| list/tuple | array  | array  |
| str        | string | string |
| int/float  | number | number |
| True       | true   | true   |
| False      | false  | false  |
| None       | null   | null   |

## RegExp对象

```js
/*
在python中如果需要使用正则 只需要借助于re模块
在js中如果需要使用正则 需要创建正则对象
*/
// 第一种方式
let reg1 = new RegExp('^[a-zA-Z][a-zA-Z0-9]{5,11}');
// 第二种方式(个人推荐)
let reg2 = /^[a-zA-Z][a-zA-Z0-9]{5,11}/;

// 匹配内容
reg1.test('zhangsan'); // 返回true

// question get alpha all a from string
let sss = 'egondsb dsb dsb';
sss.match(/s/) // 拿到第一个就停止了
// 全局匹配
sss.match(/s/g) 


// 全局匹配吐槽点
// 全局模式有一个lastIndex属性
let reg2 = /^[a-zA-Z][a-zA-Z0-9]{5,11}/g;
reg2.test('egondsb');
// true
reg2.test('egondsb');
// false
reg2.test('egondsb');
// true


// 吐槽点二
// 什么都不传,默认传的是undefined,它刚刚好符合这个格式
let reg3 = /^[a-zA-Z][a-zA-Z0-9]{5,11}/;
reg3.test()
// true
```

> 总结: 
>
> 在使用js书写正则的时候一定要注意以上两个问题
>
> 但是一般情况下也不会遇到

## Math的对象(了解)

```js
abs()	绝对值
exp()	e的指数
floor()	对数进行下舍入
log()	数的自然对数(底数为e)
max()	最大值
min()	最小值
pow()	幂
random()0-1随机数
round()	四舍五入的整数
sin()	正弦值
sqrt()	平方根
tan(X)	正切
```

# BOM与DOM

+ bom浏览器对象模型(browser object model)

  js代码操作浏览器

+ dom文档对象模型(document object model)

  js代码操作标签

# BOM操作

## window对象

```js

window.innerHeight
window.innerWidth
window.open('https://evansuner.xyz','','height=400px width=400px top=200px left=200px') // 新建窗口打开页面,第二个参数不写
window.close() // 关闭浏览器
// 创建父子页面通信Windows.opener() 了解
```

## window子对象

```js

window.navigator.appName
// "Netscape"
window.navigator.appVersion
// "5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.77 Safari/537.36 Edg/91.0.864.41"
window.navigator.userAgent //掌握
// "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.77 Safari/537.36 Edg/91.0.864.41" 用来标识当前是否是一个浏览器
/*
扩展: 防爬措施
	1. 最简单的最常用的就是校验当前请求的发起者是否是一个浏览器
		userAgent 
*/
window.navigator.platform
// "Win32"

// 如果是window的子对象 那么window可以省略不写
```

## history对象

```js
window.history.back() // 回退到上一页
window.history.forward() // 前进到下一页
// 对应的就是浏览器左上方的两个按钮
```

## location对象(掌握)

```js
window.location
window.location.href // 获取当前页的url
window.location.href = url // 跳转到指定的url
window.location.reload() // 刷新页面
```

## 弹出框

+ 警告框
+ 确认框
+ 提示框

```js
alert("警告");
confirm('确认');
prompt('提示信息','输入框');
```

## 计时器相关

+ 过一段时间后触发(一次)
+ 每隔一段时间触发(循环)

```js
 let t = setTimeout(function(){
    alert(123);
  },3000);
  clearInterval(t);

  let t1 = setInterval(function(){
    alert(123);
  },3000); // 循环任务
  clearInterval(t1);
```

# DOM操作

DOM树的概念:

所有的标签都可以称之为节点

js通过dom创建动态的HTML:

+ js能够改变页面中所有的HTML元素

+ js能够改变页面中所有HTML属性

+ js能改变页面中所有的css样式

+ js能对页面中所有的事件作出反应

DOM操作操作的是标签 而一个HTML页面上的标签有很多

1. 鲜血如何查找标签
2. 再学DOM操作标签

DOM操作需要用关键字document起手

## 查找标签

+ 直接查找(必须掌握)

  ```js
  /*
  1. id查找
  2. class查找
  3. 标签查找
  */
  document.getElementById('d2');
  document.getElementsByClassName('c1')[0];
  document.getElementsByTagName('div')[0];
  
  ```

  > 注意: 
  >
  > 三个方法的返回值是不一样的
  >
  > 当使用变量名指代标签的时候 一般情况下都推荐写成
  >
  > xxxEle
  >
  > ​	divEle  aEle

+ 间接查找(熟悉)

  ```js
  let pEle = document.getElementsByClassName('c1')[0] // 注意是否需要索引取值
  
  pEle.parentElement // 父亲节点
  pEle.children // 子节点
  pEle.children[0]
  pEle.firstElementChild // 第一个子节点 
  pEle.lastElementChild // 最后一个子节点
  pEle.nextElementSibling // 下一个毗邻的节点
  pEle.previousELementSibling // 上一个毗邻的节点
  ```

## 节点操作

```js
/*
通过dom操作动态创建img标签
并且给标签添加属性
最后将标签添加到文本当中
*/

let imgEle = document.createElement('img');
imgEle.src ="https://th.bing.com/th/id/OIP.akv1QPUqZ61pgLfurfwGPwHaLV?pid=ImgDet&rs=1"
imgEle.setAttribute('title','touxiang');
let divEle = document.getElementById('d1');
divEle.appendChild(imgEle);
let aEle = document.createElement('a');
aEle.href = "https://evansuner.xyz";
aEle.innerText = "点我跳转";
let pEle = document.getElementById('d2');
divEle.insertBefore(aEle,pEle);

/*
额外补充
appendChild()

removeChild()
replaceChild()

setAttribute()
getAttribute()
removeAttribute()
*/
```

## innerText和innerHTML的区别

innerText 可以获取标签内的所有文本

interHTML 可以获取标签和标签内的文本

但是 innerText 不能识别HTML标签

## 获取值操作

```js
// 获取用户数据标签内部的数据
let seEle = document.getElementById('d2');
seEle.value
let inputEle = document.getElementById('d1');
inputEle.value

// 如何获取用户上传的文件数据
let fileEle = document.getElementById('d3');
fileEle.value	// 无法获取到文件数据
// C:\fakepath\02.png
fileEle.files
// FileList {0: File,length: 1}
fileEle.file[0]
```

## Class CSS操作

```js
divEle.classList
divEle.classList.remove()
divEle.classList.add()
divEle.classList.contains('c1') // false or true
divEle.classList.toggle('bg_red') // 有则移除,无则添加

divEle.style.color = 'red'
divEle.style.fontSize = '28px'
```

## 事件

符合到达某个事先设定的条件 自动触发的动作

| 事件名      | 说明                               |
| ----------- | ---------------------------------- |
| onclick     | 当用户点击某个对象时调用的事件句柄 |
| ondblclick  | 当用户双击某个对象时调用的事件句柄 |
| onfocus     | 元素获取焦点                       |
| onblur      | 元素失去焦点                       |
| onchange    | 域内容被改变                       |
| onkeydown   | 某个键盘按键被按下                 |
| onkeypress  | 某个键盘按键被按下并松开           |
| onkeyup     | 某个键盘按键被松开                 |
| onload      | 一张页面或一张图像完成加载         |
| onmousedown | 鼠标按钮被按下                     |
| onmousemove | 鼠标被移动                         |
| onmouseout  | 鼠标从某元素移开                   |
| onmouseover | 鼠标移动到某元素智商               |
| onselect    | 在文本框中的文本被选中时发生       |
| onsubmit    | 确认按钮被点击,使用的对象是form    |

### 绑定事件的两种方式

```html
<button onclick="func1()">点我</button>
<button id="btn">点我</button>
<script>
    // 第一种方式	
	function func1(){
    	alert(111);
	}
    // 第二种方式
    let btnEle = document.getElementById('btn');
    btnEle.onclick = function(){
        alert(123);
    }
</script>
<!--
script标签可以放在head中也可以放在body的最后
但是放在body最后最好,因为代码是从上至下运行的,放在head中可能会导致找不到标签
-->
```

## 原生JS事件绑定

### 开关灯案例

```HTML
<script>
 	let btnEle = document.getElementById('d2');
    let lightEle = document.getElementById('d1');
    btnEle.onclick = function(){
        lightEle.classList.toggle('bg_red');
    }    
</script>
```

### input框获取焦点失去焦点

```HTML
<script>
  let inputEle = document.getElementById('d1');
  inputEle.onfocus = function(){
    inputEle.value = "";
  }
  inputEle.onblur = function (){
    inputEle.value = 'bye bye';
  }
</script>
```

### 实时展示当前时间

```HTML
<input type="text" id="d1">
<button id="btn1">开始</button>
<button id="btn2">结束</button>
<script>
  // 1. 访问页面之后 将访问的事件展示到input框中
  // 2. 动态展示当前时间
  // 定义一个全局变量 存储定时器
  let t = null
  let iEle = document.getElementById('d1');
  function showTime(){
      let currentTime = new Date();
      iEle.value = currentTime.toLocaleString();
  }

  let btn1Ele = document.getElementById('btn1');
  let btn2Ele = document.getElementById('btn2');
  btn1Ele.onclick = function (){
      // 每点击一次就会开设一个定时器,而t只指代最后一个
      // 需要定时器只能开启一次
      if(!t){
          t = setInterval(showTime,1000);
      }
  }
  btn2Ele.onclick = function(){
      iEle.value = "";
      clearInterval(t);
      // 清除定时器的同时 应该将t重置为null
      t = null;
  }
</script>

```

### 省市联动

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>省市联动</title>
</head>
<body>
<select name="" id="d1">
  <option value="pro" selected>--请选择--</option>
</select>
<select name="" id="d2"></select>

<script>
  // 模拟省市数据
  data = {
    "河北省":["廊坊","邯郸","唐山"],
    "北京":["朝阳区","海淀区","昌平区"],
    "山东省":["威海市","烟台市","淄博市"],
    "上海":["浦东新区","静安区","黄浦区"],
    "深圳":["南山区","宝安区","福田区"]
  };
  let proEle = document.getElementById('d1');
  let cityELe = document.getElementById('d2');
  for(let pro in data){
    // 将省信息做成一个个option标签 添加到第一个select框中
    let optEle = document.createElement('option');
    optEle.innerText = pro;
    optEle.value = pro; // <option value="省">省</option>
    proEle.appendChild(optEle);
  }
  // 文本域变化事件
  proEle.onchange = function(){

    // 获取到用户选择的省
    let currentPro = proEle.value;
    let currentCityList = data[currentPro];

    // 清空市中的所有option 并设置默认项
    cityELe.innerHTML = "";
    let defaultOpt = document.createElement('option');
    defaultOpt.innerText = "--请选择--";
    defaultOpt.setAttribute('selected','selected');
    cityELe.appendChild(defaultOpt);

    for (let i = 0;i<currentCityList.length;i++){
      let currentCity = currentCityList[i]
       let optEle = document.createElement('option');
       optEle.innerText = currentCity;
       optEle.value = currentCity;
       cityELe.appendChild(optEle);
    }
  }
</script>
</body>
</html>
```