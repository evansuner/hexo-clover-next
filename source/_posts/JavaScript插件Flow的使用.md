---
title: JavaScript插件Flow的使用
date: 2022-01-23 00:00:34.239
updated: 2022-01-23 00:00:34.239
url: /?p=204
categories: 
- JavaScript
tags: 
- JavaScript 
---

JavaScript是一种动态的弱语言，所以在编写代码的时候很可能会出现意想不到问题，并且要想找到错误，必须要在代码执行之后才能找到。使用Flow插件，可以给JavaScript以添加注释的形式模拟成静态语言。
## Flow 
> Flow is a static type checker for your JavaScript code. It does a lot of work to make you more productive. Making you code faster, smarter, more confidently, and to a bigger scale. --from official site  
```javascript
// @flow
function sum(a: number, b: number){
    return a + b
}
sum(100, 50) // ok
sum('100', 50) // type error
```

### Flow安装
```bash
# 作为项目依赖安装，不使用全局
yarn add flow-bin --dev
```
### Flow使用   
#### 声明
```javascript
// 在项目在开头进行声明
// @flow
...
```
#### 配置  
![16426931768314](https://mweb-cdn.reidosann.top/mweb/16426931768314-20220120-16426931768321.jpg)

#### 启动flow服务检查
```bash
➜  test yarn flow
Error ┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈ weakly-typed.js:6:5

Cannot call sum with "100" bound to a because string [1] is incompatible with
number [2]. [incompatible-call]

 [2] 2│ function sum(a: number, b: number){
     3│     return a+b
     4│ }
     5│ sum(100,100)
 [1] 6│ sum("100",100)

Found 1 error
error Command failed with exit code 2.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```
使用flow如果一直使用命令行进行检查一定是非常的麻烦的，这里推荐使用flow vscode插件，这里选择Facebook flow官方的插件，使用时需要保存   

#### 关闭flow服务
```bash
➜  test yarn flow stop
yarn run v1.22.17
warning package.json: No license field
$ /Users/Reido/Documents/Developer/VSCode/test/node_modules/.bin/flow stop
Trying to connect to server for `/Users/Reido/Documents/Developer/VSCode/test`
Told server for `/Users/Reido/Documents/Developer/VSCode/test` to die. Waiting for confirmation...
Successfully killed server for `/Users/Reido/Documents/Developer/VSCode/test`
✨  Done in 2.44s.
```
#### 移除类型注解
在我们编码完成之后需要将代码中的注解全部移除，否则会无法运行
使用`yarn add flow-remove-types --dev`提供的命令行工具自动移除类型注解
+ 方式一(官方)
```bash
## 项目完成编码过后我们将移除类型注解的代码全部转移至dist目录下
yarn flow-remove-types . -d dist
```
+ 方式二(主流方式)
```bash
yarn add @babel/core @babel/cli @babel/preset-flow --dev
## 在项目根目录下创建一个src文件夹，将源代码移入其中
## 在根目录下创建一个 .babelrc 配置文件
{
    "presets":["@babel/preset-flow"]
}
## 在命令行中去除类型注释,将src下的源代码去除注释后的代码放在dist下
yarn babel src -d dist
```
<font style="color:red">【注】</font>  
此时，我们注释的number或者其他类型下面会出现红线，我们需要进入vscode设置，将JavaScript校验关闭
![16426913764925](https://mweb-cdn.reidosann.top/mweb/16426913764925-20220120-16426913764934.jpg)

#### flow 类型推断
根据代码使用情况，代码会提示缺少什么样的注释   

#### flow 类型注解   
flow的注解不仅可以使用在函数的参数上，还可以使用在**变量的声明过程中**还有**函数的返回值**
```javascript
// 变量声明
let num: number = 100
// num = 'string' // 会报错

// 函数返回值
function foo(): number {
    // return 100
    // return 'string' // 会报错
}

// 函数没有返回值的时候需要将其标记为 void
function foo(): void {
    ...
}
```
#### flow 原始类型
```javascript
/**
 * 原始类型
 * @flow
 */

const a: string = 'foobar'
const b: number = 100 // Infinity // NaN
const c: boolean = false // true
const d: null = null
const e: void = undefined
const f: symbol = Symbol()
```
#### flow 数组类型
```javascript
/**
 * 数组类型
 * 两种方式
 * @flow
 */

const arr1: Array<number> = [1,2,3]
const arr2: number[] = [1,2,3]

// 元组
const foo: [string,number] = ['foo',1]
```
#### flow 对象类型
```javascript
/**
 * 对象类型
 * @flow
 */

// 指定对象有两个键值对，对应其类型
const obj1: { foo: string, bar: number } = { foo: "zhangsan", bar: 18 }

// 指定对象有两个键值对，foo可选
const obj2: {foo?: string, bar:number} = {bar:100}

// 不指定对象的键值对类型
const obj3 = {}
obj3.key1 = 'value1'
obj3.key2 = 100

// 指定对象的键值对所需的类型
const obj4: {[string]:string} = {}
obj4.key1 = 'value1'
obj4.key2 = 'value2'
```
#### flow 函数对象
```javascript
/**
 * 函数类型
 * @flow
 */

// 利用箭头函数，类似于函数签名的形式
function foo(callback: (string,number)=>void) {
    // 接受两个参数，但是不允许有返回值,返回undefined
    callback('string', 100)
}
```
#### flow 特殊类型
```javascript
/**
 * 特殊类型
 * @flow
 */

import { type } from "os";

const a: 'foo' = 'foo'
// const b: 'foo' = 'foo1' // 报错
// 一般用于以下情况，限制type这个变量只能有以下这三个值
// const type: 'success' | 'warning' | 'danger' = 'other' // 报错

const c: string | number = '' // 100

type StringOrNumber = string | number
const d: StringOrNumber = 'string' // 100

// maybe类型
const gender: ?number = undefined
// 相当于
const gender1: number | null | void = undefined
```
#### flow 任意类型
```javascript
/**
 * 任意类型
 * Mixed Any
 * @flow
 */

// string|number|boolean|...
// Mixed是一个强类型
import { type } from "os";

function passMixed(value: mixed) {
    // value.substr(1) // error
    // value * value   // error
    if (typeof value === 'string') {
        value.substr(1) // ok
    }
    if (typeof value === 'number') {
        value * value // ok
    }
}
passMixed('string')
passMixed(100)

// -----------------------------
// Any任然是一个弱类型的
function passAny(value: any) {
    value.substr(1) // ok
    value * value   // ok
}
passAny('string')
passAny(100)
```
#### flow 类型小结
+ [Facebook Flow官网对于types的描述文档](https://flow.org/en/docs/types/)   
+ [更直观的第三方手册](https://www.saltycrane.com/cheat-sheets/flow-type/latest/)

#### flow 运行环境API
```javascript
/**
 * 运行环境API
 * @flow
 */

// document.getElementById('app') //ok
// document.getElementById(100) // error
// 如果找不到app这个对象，就返回 null
const element: HTMLElement | null = document.getElementById('app')
```