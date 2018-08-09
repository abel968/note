<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

* [这是一篇MarkDown笔记](#这是一篇markdown笔记)
	* [基本语法](#基本语法)
		* [图像](#图像)
	* [高级用法](#高级用法)
		* [任务列表](#任务列表)
		* [表格扩展](#表格扩展)
		* [Emoji](#emoji)
		* [上下标](#上下标)
		* [脚注](#脚注)
		* [缩略](#缩略)
		* [标记](#标记)
		* [CriticMarkup](#criticmarkup)
		* [数学](#数学)
		* [目录](#目录)
		* [导入文件](#导入文件)
		* [Code Chunk](#code-chunk)

<!-- /code_chunk_output -->
# 这是一篇MarkDown笔记
**vscode中安装Markdown Preview Enhanced**

## 基本语法
*斜体*
**粗体**
***斜体加粗体***
~~删除线~~

>在行首加入&lt;表示引用
>>引用可以嵌套

- 无序列表1
- 无序列表2

1. 有序列表1
1. 有序列表2

`内联代码`
```{.line-numbers}
这是代码区块
方法是两个```之间
在第一个```后面加语言名字，如python，可以提供语法高亮
在第一个```后面加{.line-numbers}，可以显示行数
```
    这也是代码区块
    方法是行首4个空格
***
使用三个* _ 或者 - 可以出现分割线
[链接](地址)
[链接](地址 "Option title")
上面的为行内式子，也可以用参考式
```
[链接][id]
然后在任意空白位置定义：
[id]: 地址 "Option title"
```
###图像
![Alt text](/path/to/img.jpg)
![Alt text](/path/to/img.jpg "Option title")

| Item1                                | Item2                              | Item3 |
| :----------------------------------- | ---------------------------------: | :---: |
| 居左                                 | 居右                               | 居中  |
| 可以把第一行去掉，做成没有表头的表格 | 冒号表示居哪，没有冒号为默认的居左 |

## 高级用法
### 任务列表
- [x] 任务1已完成
- [ ] 任务2未完成
### 表格扩展
a | b 
--|--
> | 1
2 |
1 | 2
^ | 4
### Emoji
:smile:
:fa-car:
:cry:
### 上下标
30^th^
H~2~
### 脚注
Content [^1]
[^1]: Hi! This is footnote.
### 缩略 
*[HTML]: Hyper Text Markup Language
*[W3C]:  World Wide Web Consortium
The HTML specification
is maintained by the W3C.
### 标记
==marked==
### CriticMarkup
默认缺省禁用。且仅用于markdown-it parser, 不与pandoc parser 兼容。
### 数学
- $f(x) = sin(x) + 12$
`$f(x) = sin(x) + 12$`数学表达式会在行内显示
- $$\sum_{n=1}^{100} n$$
`$$\sum_{n=1}^{100} n$$`或者使用```math数学表达式会在块内显示
### 目录
使用`Markdown Preview Enhanced: Create Toc`可以创建目录。若想在目录中排除一个标题，在标题后面加`{ignore=true}`
### 导入文件
使用方式： `@import "file"`
可以引用在线文件
引用图片时，可以设置大小。方式为：
`@import "pic.png" {width="300px" height="200px" title="title" alt="My alt"}`
引用文件作为Code Chunk 如：
`@import "test.py" {cmd="python3"}
### Code Chunk


