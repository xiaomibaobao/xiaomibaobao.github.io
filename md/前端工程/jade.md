##jade
## 1.文档声明和头尾标签
```
doctype html
html
   head
      title xiaominode.js
   body
```

## 2.命令行实时编译
```
jade index.jade 命令行进行编译
jade  -P index.jade 美化缩进
jade  -P -w index.jade 实时监控
```

## 3.标签语法
```
doctype html
html
   head
      title xiaominode.js
   body
      h1 welcome to xiaomi to study node.js
```

## 4.属性文本和值
```
#id.classname
h1.title(id='myid',class='myclass') xiaomi
```

## 5.混合的成段文本和标签
```
style.
         body {background-color: orange}
         
p
         | 1.a
         | 2.b
         | 3.c
         | 4.d        
```

## 6. for each
```
 - var courses = ['node.js','jade']
 each item in courses
    p=item
```

## 7. 模板之间的继承和包含
1. 模板的继承,通过block extends实现。块看做block,实现递归。
2. 可以定义，可以多次调用
3. 子文档里可以覆盖父文档里的东西

head.jade
```
meta(charset='utf-8')
title i am title
```

layout.jade
```
//-extends layout
   1.如果哪个模板继承了layout,就可以把它里面的block替换 block desc
head
    include head
    include title.html
body
   block desc
     p desc from layout
   block title
   block content
   #tail I am tail
```

extends.jade
```
extends layout.jade
block title
  p I am a title
block content
  p I am the content
block desc
    p desc from extends
```