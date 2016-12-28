## less
Less 是一门一种<span class="text-danger">动态</span>样式 语言，它扩充了CSS语言，增加了诸如<span class="text-danger">变量</span>、<span class="text-danger">混合（mixin）</span>、<span class="text-danger">函数</span>等功能，让 CSS 更易维护、方便制作主题、扩充。

## 客户端使用
[下载地址](https://github.com/less/less.js/archive/master.zip)

### 外联样式
引入你的 .less 样式文件的时候要设置 rel 属性值为 “stylesheet/less”:
```css
<link rel="stylesheet/less" type="text/css" href="styles.less">
```

### 内联样式如下：
```css
<style type="text/less">
  // less 代码
</style>
```
接下来 下载 less.js 并通过 <script></script> 标签将其引入，放置于页面的 <head> 元素内：
```css
<script src="less.js" type="text/javascript"></script>
```
> 注意：你的less样式文件一定要在引入less.js前先引入。


## 变量
Less中的变量允许你在样式中的某个地方对常用值进行定义，然后应用到样式中，这样只要改变你定义的变量参数值就可以达到改变全局的效果
```css
@base:red;
body{color:@base}
```

## 混合
在LESS中我们可以定义一些通用的属性集为一个class，然后在另一个class中去调用这些属性
```css
.bordered {
  border-top: solid 1px red;
  border-bottom: solid 2px green;
}

.div1 {
  color: #111;
  .bordered;
}

.div2 {
  color: red;
  .bordered;
}
```

## 带参数混合
在LESS中，你还可以像函数一样定义一个带参数的属性集合
```css
.border-radius (@radius: 5px) {
     border:1px solid red;
     border-radius: @radius;
     width:300px;
}
.div1 {
    .border-radius(0px);
}
.div3 {
    .border-radius;
}
.div2 {
    .border-radius(20px);
}
```

## arguments
@arguments在Mixins中具是一个很特别的参数，当Mixins引用这个参数时，他将表示所有的变量
```css
.pad(@top,@right,@bottom,@left) {
  padding:@arguments;
}
div{
  width:400px;
  border:1px solid red;
  .pad(10px,50px,100px,40px)
}
p
{
  background-color: green;
}
```

## 模式匹配
有些情况下，我们想根据传入的参数来改变混合的默认呈现
```css
.mixin (red, @color) {
    color: red;
}
.mixin (green, @color) {
    color: green;
}
.mixin (@_, @color) {
    display: block;
}
@switch: red;
.div1 {
    .mixin(@switch, #888);
}
```

## 导引表达式
当我们想根据表达式进行匹配，而非根据值和参数匹配时，导引就显得非常有用
```css
.mixin (@a) when (@a =red) {
  background-color: red;
}
.mixin (@a) when (@a =green) {
  background-color: green;
}
.mixin (@a) {
  color: @a;
}
.red { .mixin(red) }
.green { .mixin(green) }
```

## 嵌套规则
LESS 可以让我们以嵌套的方式编写层叠样式
```css
#dvi1 {
  color: red;

  p {
    font-size: 12px;
  }

  .logo {
    width: 300px;
    &:hover {
      text-decoration: none
    }
  }
}
```
>注意 & 符号的使用—如果你想写串联选择器，而不是写后代选择器，就可以用到&了. 这点对伪类尤其有用如 :hover 和 :focus,&表示上一级元素