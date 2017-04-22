# 基础语法
## 标题
```
# This is an <h1> tag
## This is an <h2> tag
### This is an <h3> tag
```
# This is an `<h1>` tag
## This is an `<h2>` tag
### This is an `<h3>` tag
## 加粗/斜体/删除线
### 斜体
```
*This text will be italic*
_This text will be italic_
```
*This text will be italic*  
_This text will be italic_  
### 加粗
```
**This text will be blod**
__This text will be blod__
```
**This text will be blod**  
__This text will be blod__
### 组合
_You **can** <del>combine<del> ~~them~~_
## 列表
### 有序
```
1. Item1
1. Item2
    1. Item2a --一个Tab
    1. Item2b
```
1. Item1
1. Item2
    1. Item2a --一个Tab
    1. Item2b
### 无序
* Item1
* Item2
    * Item2a
    * Item2b
## 图片
```
![CSDN Logo](/images/logo.png)
Formate:![Alt Text](url)
```
![CSDN Logo](http://c.csdnimg.cn/public/favicon.ico)
## 链接
```
[GitHub](http://github.com) -automatic!
Formate:[Text](url)
```
[GitHub](http://github.com) -automatic!
## 引用
```
As Kanye West said:

> We're living the future so
> the present is our past.
```
As Kanye West said:

> We're living the future so    
> the present is our past.
## 行内代码
```
I think you should use an `<addr>` element here instead.
```
I think you should use an `<addr>` element here instead.
## 语法高亮

```javascript
function fancyAlert(arg) {
  if(arg) {
    $.facebox({div:'#foo'})
  }
}
```
### 使用四个空格
    function fancyAlert(arg) {
      if(arg) {
        $.facebox({div:'#foo'})
      }
    }
## 任务列表
```
- [x] @mentions, #refs, [links](), **formatting**, and <del>tags</del> supported
- [x] list syntax required (any unordered or ordered list supported)
- [x] this is a complete item
- [ ] this is an incomplete item
```
- [x] @mentions, #refs, [links](), **formatting**, and <del>tags</del> supported
- [x] list syntax required (any unordered or ordered list supported)
- [x] this is a complete item
- [ ] this is an incomplete item
## 表格
```
列1|列b
-|-
adjfadofafoa|dfasdfjkladfjajkkfklas
```
列1|列b
-|-
adjfadofafoa|dfasdfjkladfjajkkfklas
# 参考链接
参考[Github MarkDown语法](https://guides.github.com/features/mastering-markdown/#examples)