---
title: 06 bootstrap入门
date: 2020-08-14 02:17:46.0
updated: 2021-11-09 08:22:16.352
url: /?p=16
categories: 
- 前端
tags: 
- bootstrap
---

# Bootstrap简介

概况就爱已经写好了很多页面样式,使用的时候,只需要下载对应的文件即可

在使用Bootstrap的时候所有的页面样式只需要通过class来调节

# 全局CSS样式

## 布局容器

```html
<div class="container"></div>
<div class="container-fluid"></div>
```

## 栅格系统

```html
<div class="container">
<div class="row"> <!-- row会将container的一行分为12等份-->
  <div class="col-md-12"></div>
</div>
</div>

<!--为了适应响应式布局,bootstrap使用一下栅格参数来适配不同大小屏幕的变化-->
col-xs-6	col-sm-6	col-md-6	col-lg-6

<!--要想实现居中显示12等份中的一部分,我们可以使用offset-->
<div class="col-md-8 col-md-offset-2"><div>
<!--这个地方我们需要注意的是-->
    col-md-offset-* 只在bootstrap3版本适用, bootstrap4以及其之后的版本将其变更为 offset-*	或者offset-md-*
```

## 排版

bootstrap将所有的原生HTML标签的文本字体统一设置成了肉眼可以接受的样式

## 表格

```html
<!--table样式-->
先在table标签中添加class属性值 table
条纹状表格: table-striped
带边框表格: table-bordered
鼠标悬停:   table-hover
锁紧表格:   table-condensed		padding均会减半

<!--给表格的行或单元格设置颜色-->
<!-- On rows -->
<tr class="active">...</tr>
<tr class="success">...</tr>
<tr class="warning">...</tr>
<tr class="danger">...</tr>
<tr class="info">...</tr>

<!-- On cells (`td` or `th`) -->
<tr>
  <td class="active">...</td>
  <td class="success">...</td>
  <td class="warning">...</td>
  <td class="danger">...</td>
  <td class="info">...</td>
</tr>
```

## 表单

```html
<!--针对表单标签 加样式适用form-control-->
<div class="container">
  <div class="col-md-4 offset-4">
    <h2 class="text-center">登陆页面</h2>
      <!--针对报错信息 可以加has-error(input的父标签)-->
    <p class="has-error">username:
      <input type="text" class="form-control">
    </p>
    <p>password:
      <input type="password" class="form-control">
    </p>
    <p>
      <select name="" id="" class="form-control">
        <option value="">111</option>
        <option value="">222</option>
        <option value="">333</option>
      </select>
    </p>
    <textarea name="" id="" cols="30" rows="10" class="form-control"></textarea>

  </div>
</div>
```

## 按钮

```HTML
<!--
首先添加上属性值 btn
其次它的属性值还可以添加上
btn-primary
btn-secondary
btn-success
btn-danger
btn-warning
btn-info
btn-light
btn-dark
-->

<!--
除此之外,btn还可以调节大小
btn-lg
btn-sm
btn-block	占满父元素宽度
-->
```