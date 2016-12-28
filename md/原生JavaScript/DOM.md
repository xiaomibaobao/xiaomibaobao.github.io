# DOM
>+ HTML DOM 定义了访问和操作 HTML 文档的标准方法。
>+ DOM 以树结构表达 HTML 文档。
>+ DOM树：
<img width="840" src="/img/domtree.png" />

### 1. DOM 节点
DOM可以将html或者xml（xml中可以自己扩展标签。）标签渲染成节点。每个节点都有自己的属性和方法。是具有层次结构的节点。nodeType用来检测节点类型
##### 1.1文档节点。
整个页面只有一个文档节点（根节点）document。  一个文档节点只有一个元素节点：就是html
- nodetype：9；
- nodename：“#document”；
- nodevalue：null；
##### 1.2注释节点：
注释中的内容会被渲染成注释节点。
- nodetype：8；
- nodename：“#comment”；
- nodevalue：注释的内容；
##### 1.3文档节点。
- nodetype：3；
- nodename：“#text”；
- nodevalue：文本内容包括回车和空格；
##### 1.4元素节点 即标签。
- nodetype：1；
- nodename：“#”+大写的标记名
- nodevalue：null；   
### 2 DOM中的节点关系
`firstChild`
`firstElementChild`(IE6-8不支持)
`lastChild`
`firstElementChild`(IE6-8不支持)
`previousSibling`哥哥节点， 同firstChild，包括注释节点和文本节点（回车空格等）都是最近的一个。
`previousElementSibling`哥哥元素节点，(IE6-8不支持)
`nextsSibling`
`nextElementSibling`(IE6-8不支持)
`childNodes`获得所有的子节点。
`children`  所有的子元素节点，ie6-8会把文本节点（不包含回车和空格）当成元素节点。
`parentNode ` 父节点，
节点未找到，则返回null
```
//children:获取当前元素的所有子节点
    function children(curEle, tagName) {
        var ary = [];
        if (flag) {
            ary = this.listToArray(curEle.children);
        } else {
            var chs = curEle.childNodes;
            for (var i = 0; i < chs.length; i++) {
                var curNode = chs[i];
                if (curNode.nodeType == 1) {
                    ary.push(curNode)
                }
            }
        }
        if (typeof tagName == 'string') {
            for (var i = 0; i < ary.length; i++) {
                if (ary[i].nodeName.toLowerCase() !== tagName.toLowerCase()) {
                    ary.splice(i, 1);
                    i--;
                }
            }
        }
        return ary;
    }
    //prev:获取上一个哥哥元素
    function prev(curEle) {
        if (flag) {
            return curEle.previousElementSibling;
        }
        var pre = curEle.previousSibling;
        while (pre && pre.nodeType !== 1) {
            pre = pre.previousSibling;
        }
        return pre;
    }

    //prevAll:获取所有的哥哥元素节点
    function prevAll(curEle) {
        var pre = this.prev(curEle);
        var ary = [];
        while (pre) {
            ary.unshift(pre);
            pre = this.prev(pre);
        }
        return ary;
    }
    //getCss:获取非行间样式
    function getCss(curEle, attr) {
        var val = null;
        var reg = null;
        if (flag) {//高级浏览器
            val = getComputedStyle(curEle, null)[attr];
        } else {//低级浏览器
            if (attr == 'opacity') {// alpha(opacity=10)
                val = curEle.currentStyle['filter'];
                reg = /^alpha\(opacity[=:](\d+(?:\.\d+))?\)$/i;
                return reg.test(val) ? reg.exec(val)[1] / 100 : 1;
            } else {
                val = curEle.currentStyle[attr];
            }

        }
        reg = /^([+-]?\d+(\.\d+)?)(px|pt|em|rem)?$/i;//-200px +200px 22.33px px pt em rem
        // reg=/^((\+|-)?\d+(\.\d+)?)(px|pt|em|rem)?$/i;
        return reg.test(val) ? parseFloat(val) : val;
    }

    //setCss:设置行间样式
    function setCss(curEle, attr, value) {
        //float
        if (attr == 'float') {
            curEle.style.cssFloat = value;//火狐
            curEle.style.styleFloat = value;//ie
            return;
        }
        //透明度的处理
        if (attr === 'opacity') {
            curEle.style.opacity = value;
            curEle.style.filter = 'alpha(opacity=' + value * 100 + ')';
            return;
        }
        //加单位的处理；
        var reg = /(width|height|top|right|bottom|left|((margin|padding)(top|right|bottom|left)?))/
        if (reg.test(attr)) {
            value += 'px';
        }
        curEle.style[attr] = value;
    }
```

### 3 DOM常用的操作元素的方法
DOM提供了操作页面元素常用的属性和方法。
`document.getElementById()`
`document.getElementsByTagName("p"),`通过html标名获得一组元素
`document.getElementsByName("a")`通过name属性值获得一组元素。（主体只能是document,IE不兼容非表单元素的name属性）
`document.querySelector(".p1"),`只能获得一个。是通过CSS选择器才行。
`document.querySelectorAll("ul>ul")`一组全部。
`document.body `可以直接获得Body，
`document.documentElement   ` 直接获得html元素。
`document.getElementsByClassName("a")`通过类名获得元素。IE6-8不兼容。
```
//getByClass:在一定范围内通过className获取元素
    function getByClass(curEle, strClass) {
        if (flag) {//高级
            return curEle.getElementsByClassName(strClass);
        }
        var ary = [];
        var aryClass = strClass.replace(/(^\s+)|(\s+$)/g, '').split(/\s+/g);
        var nodeList = curEle.getElementsByTagName('*');//拿到当前元素下所有元素
        for (var i = 0; i < nodeList.length; i++) {//循环：目的是为了匹配每个元素的className是否符合要求，匹配要求：是这个元素上的className符合aryclass中的每一个className字符串
            var curNode = nodeList[i];
            var bOk = true;//假设法：假设都符合
            for (var k = 0; k < aryClass.length; k++) {
                var curClass = aryClass[k];
                var reg = new RegExp('(^| +)' + curClass + '( +|$)');
                if (!reg.test(curNode.className)) {
                    bOk = false;
                }
            }
            if (bOk) {
                ary.push(curNode)
            }
        }
        return ary;
    }
```
### 4 DOM深入：动态操作DOM。
`creatElement` 创建一个节点
`creatTextNode` 创建文本节点。
`appendChild  ` 把节点添加到页面末尾的位置上（主体只能是操作元素的父标签）
`insertBefore（newEle，OldEle）`把新的插入到旧的之前
`replaceChild`
`removeChild`
`cloneNode `深克隆：包括克隆其后代，没写参数或者false，浅克隆，只克隆本身。
`getAttribute  `  获取自定义属性
`setAttribute  ` 设置自定义属性（特殊属性、固有属性、自定义都可以。），通过`setAttribute`设置的属性名和值，只能通过getAttribute来获取。 class在ie6-7能设置成功，但是样式不起作用。
`removeAttribute`

##### 4.1 操作属性的方式：
添加系统不认识的属性，可以操作和修改，但看不见。只能通过getAttribute    setAttribute removeAttribute
1，创建元素：
a，`document.creatElement`
b,   `obj.cloneNde(boolean)`
2,插入元素。
a,  `oParent.appendChild(创建的)`
b，`insertBefore（new，old）`
3，删除元素
`oParent.removeChild`（要删除的元素）
4,替换元素
`oParent.replace(newEle,oldEle=)  ` 和字符串替换相反，字符串的是`str.replace("oldStr","newStr")`

### 5 DOM 回流 重绘 映射
##### 5.1 DOM回流：
当页面结构发生改变（添加、删除、位置移动。。。margin，width。。。）的时候，浏览器需要重新计算页面结构，
例子：动态创建元素的方式。 页面性能较低，DOM回流太多。
```
for(var i=0;i<jsonDate.length;i++){
      var oLis=document.createElement('li');
      oLis.innerHTML=jsonDate[i].content;
      oUl.appendChild(oLis);//每次循环都会回流一次。
  }
```

##### 5.2 DOM重绘，
元素的样式上发生改变（字体颜色，大小，背景等。。），浏览器只需要重新对当前元素渲染
例子：字符串拼接：
```
var str='';
   for(var i=0;i<jsonDate.length;i++){
       str += "<li>"+jsonDate[i].content+"<li>"
   }
   oUl.innerHTML+=str;
```
##### 5.3 dom映射 :
当获取的元素/元素集合，和页面上的标签/容器 是一一对应的，当一个发生变化，另一个也会发生变化。
最常见的是：`appendChild  insertBefore `,如果在页面上已经有这个标签了，则移动位置，没有这个标签的时候，才是吧元素添加到 最后/最前 的位置。
```
<li>张浩</li>
<li>陈旭</li>
<li>李鹏飞</li>
<li>曹晓阳</li>
<li>111111</li>   // appendedChild
oUl.appendChild(oLis[1]);结果是：
<li>张浩</li>
<li>李鹏飞</li>
<li>曹晓阳</li>
<li>111111</li>   // appendedChild
<li>陈旭</li> //陈旭 到了最后一个,由于DOM映射，还是五个。
```

### 6 DOM盒子模型
css盒子模型： 设定好的宽高+padding+border+margin
JS盒子模型：用JS提供的属性和方法去获取元素的各种样式值；
##### 6.1 S提供的属性和方法
    client系列： clientWidth clientHeight clientLeft clientTop;
    offset系列：offsetWidth offsetHeight offsetLeft offsetTop offsetParent;
    scroll系列： scrollWidth scrollHeight  scrollLeft scrollTop;
##### 6.2  JS提供的属性的详细解读
1)client系列
  构成：
    `clientWidth/height:`设定的宽（高）+左右padding（上下padding）;
    `clientLeft/Top`:border的宽度；
  是否受内容溢出的影响：不受溢出影响；
2）offset系列：
  构成：
   ` offsetWidth/height: `设定宽（高）+左右padding（上下padding）+border
                 clientWidth+border
 ` offsetLeft:`当前元素的外边框距离定位父级的内边框的距离；
  是否受内容溢出的影响：不受溢出内容的影响；
   ` offsetParent`定位父级
3）scroll系列
   构成:
 `  scrollHeight`约等于上padding+真实内容的高度
	`scrollTop/scrollLeft:`被浏览器卷去的距离。
 >+   为什么是约等于：
   1）同一元素，不同浏览器测试的值不同；
   2）同一元素，是否溢出隐藏，在同一浏览器下也不相同；
   区分结构父级和定位父级：如果找不到，两个结果都会出现null、
   
>+ 结构父级的祖师爷：标签：htmlElement， 如果不是标签：htmlDocument； parentNode。
>+ 定位父级的祖师爷：body，小于htmlElement，
>+ 5.发现4个小问题：
1）对浏览器盒子模型的兼容处理（获取和设置）win;
2）关于clientWidth（height），offsetWidth等只能拿到四舍五入的整数，不能取到小数
3）以上涉及到的每种属性只能拿到复合值，无法拿到单独的高或者宽或者其他样式：
(通过style只能拿到行间样式，不能拿到非行间样式) getCss;
4)关于offset，当前元素的外边框距离有定位父级的内边框之间的距离;但我们没法知道，在页面多层嵌套及定位的情况下，该元素距离body的距离；

### 7.浏览器兼容处理思想
1）浏览器异常捕获兼容处理 try...catch
缺陷：无论浏览器是否支持，都会走一遍try里的JS语句，性能相对来说差；
2）属性判断
    1.attr in obj
    2.obj.attr
    3. type of obj.attr==='function'
3)浏览器版本判断 navigator.userAgent:可以打印出浏览器的详细信息，是个字符串；
    1.reg.test(navigator.userAgent)
    2.navigator.userAgent.search(reg)!=-1;找见了；
    navigator.userAgent.search(reg)===-1;没找见；