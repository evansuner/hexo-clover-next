---
title: JavaScript语言类型分析
date: 2022-01-22 23:45:54.787
updated: 2022-01-22 23:49:50.863
url: /?p=203
categories: 
- JavaScript
tags: 
- JavaScript 
---

## 类型安全
+ 强类型与弱类型   
强类型语言层面上限制函数的实参类型必须与形参类型相同，强类型当中不允许有任何隐式类型转换   
```Typescript
class Main{
    static void foo(int num){
        System.out.println(num);
    }
    public static void main(Stringp[] args){
        Main.foo(100); // ok
        Main.foo("100"); // error "100" is a string
        Main.foo(Integer.parseInt("100")); // ok
    }
}
```
弱类型语言层面上不会限制实参的类型   
```javascript
function foo(num){
    console.log(num);
}
foo(100); //ok
foo("100"); // ok
foo(parseInt("100")); // ok
```

## 类型系统
+ 静态类型与动态类型   
静态类型：变量声明的时候它的类型就已经明确了，声明过后它的类型就不允许再被修改
动态类型：反而异之
```javascript
var foo = 100
foo = 'bar' // ok
console.log(foo)
```
![types_languages](https://mweb-cdn.reidosann.top/mweb/types_languages-20220120-16426914713592.png)
 
## JavaScript类型系统特征   
弱类型且动态类型，丢失了类型系统的可靠性   

## 强类型的优势
1. 在编写代码的阶段就能发现错误
2. 代码更智能，编码更准确
3. 重构更牢靠
4. 减少不必要的类型判断