<!--
author: wngn123
head: head.png
date: 2016-08-31
title: Markdown学习
tags: markdown
category: Other
status: publish
summary:Markdown学习，Markdown基础语法。
-->

## 标题 ##
```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题`
##### 五级标题
###### 六级标题
```

## 列表 ##
熟悉 HTML 的同学肯定知道有序列表与无序列表的区别，在 Markdown 下，列表的显示只需要在文字前加上 - 或 * 即可变为无序列表，有序列表则直接在文字前加 1. 2. 3. 符号要和文字之间加上一个字符的空格。

#### 无序列表
```
	- 无序列表
	- 无序列表
	* 无序列表
	* 无序列表
```
- 无序列表
- 无序列表
* 无序列表
* 无序列表
#### 有序列表
```
	1. 无序列表
	2. 无序列表
	3. 无序列表
```
1. 无序列表
2. 无序列表
3. 无序列表

## 引用 ##
如果你需要引用一小段别处的句子，那么就要用引用的格式。只需要在文本前加入 ```>``` 这种尖括号（大于号）即可

```
> 例如这样
```
> 例如这样

## 图片与链接 ##
插入链接与插入图片的语法很像，区别在一个 `!`号
```
[baidu](www.baidu.com)
```
[baidu](www.baidu.com)

```
![image](https://raw.githubusercontent.com/wngn123/test/master/img/head.png)
```
![image](https://raw.githubusercontent.com/wngn123/test/master/img/head.png)

## 粗体与斜体 ##

Markdown 的粗体和斜体也非常简单，用两个 `*` 包含一段文本就是粗体的语法，用一个 `*` 包含一段文本就是斜体的语法。

```
** 粗体 **
* 斜体 *
```
**粗体**
*斜体*

## 表格 ##
```
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | 1600 |
| col 2 is      | centered      |   12 |
| zebra stripes | are neat      |    1 |
```
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | 1600 |
| col 2 is      | centered      |   12 |
| zebra stripes | are neat      |    1 |

 ### 代码框 ###
如果你是个程序猿，需要在文章里优雅的引用代码框，在 Markdown 下实现也非常简单，只需要用两个 \` 把中间的代码包裹起来，如 \`code\`。

```
``` python

code

``````

``` python
@requires_authorization
def somefunc(param1='', param2=0):
    '''A docstring'''
    if param1 > param2: # interesting
        print 'Greater'
    return (param2 - param1 + 1) or None
class SomeClass:
    pass
>>> message = '''interpreter
... prompt'''
```
## 分割线 ##
分割线的语法只需要另起一行，连续输入三个星号 \*** 即可。
***


