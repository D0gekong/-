

# 从0开始学习html记录（1）

## 什么是HTML?

HTML 是用来描述网页的一种语言。

- HTML 指的是超文本标记语言: **H**yper**T**ext **M**arkup **L**anguage

- HTML 不是一种编程语言，而是一种**标记**语言

- 标记语言是一套**标记标签** (markup tag)

- HTML 使用标记标签来**描述**网页

- HTML 文档包含了HTML **标签**及**文本**内容

- HTML文档也叫做 **web 页面**

  ### HTML标签

  HTML 标记标签通常被称为 HTML 标签 (HTML tag)。

  - HTML 标签是由*尖括号*包围的关键词，比如 <html>
  - HTML 标签通常是*成对出现*的，比如 <b>  和 </b>
  - 标签对中的第一个标签是*开始标签*，第二个标签是*结束标签*
  - 开始和结束标签也被称为*开放标签*和*闭合标签*

  <标签>内容</标签>

  ### HTML元素

  "HTML 标签" 和 "HTML 元素" 通常都是描述同样的意思.

  但是严格来讲, 一个 HTML 元素包含了开始标签与结束标签，如下实例:

  HTML 元素:

  <p>这是一个段落。</p>

#### Web浏览器

Web浏览器是用于读取HTML文件，并将其作为网页显示。

浏览器并不是直接显示的HTML标签，但可以使用标签来决定如何展现HTML页面的内容给用户：

![img](https://www.runoob.com/wp-content/uploads/2013/06/html-first.png)



## HTML 网页结构

下面是一个可视化的HTML页面结构：

<html>

<head>

<title>页面标题</title>

</head>

<body>

<h1>这是一个标题</h1>

<p>这是一个段落。</p>

<p>这是另外一个段落。</p>

</body>

</html>

## <!DOCTYPE> 声明

<!DOCTYPE>声明有助于浏览器中正确显示网页。

网络上有很多不同的文件，如果能够正确声明HTML的版本，浏览器就能正确显示网页内容。

doctype 声明是不区分大小写的，以下方式均可：

<!DOCTYPE html>

<!DOCTYPE HTML>

<!doctype html>

<!Doctype Html>

------

## 通用声明

### HTML5

<!DOCTYPE html>

### HTML 4.01

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">

### XHTML 1.0

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

------

## 中文编码

目前在大部分浏览器中，直接输出中文会出现中文乱码的情况，这时候我们就需要在头部将字符声明为 UTF-8 或 GBK。