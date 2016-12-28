#node入门(1)
### JavaScript与Node.js
>JavaScript最早是运行在浏览器中，然而浏览器只是提供了一个上下文，它定义了使用JavaScript可以做什么，但并没有“说”太多关于JavaScript语言本身可以做什么。事实上，JavaScript是一门“完整”的语言： 它可以使用在不同的上下文中，其能力与其他同类语言相比有过之而无不及。
>
Node.js事实上就是另外一种上下文，它允许在后端（脱离浏览器环境）运行JavaScript代码。
要实现在后台运行JavaScript代码，代码需要先被解释然后正确的执行。Node.js的原理正是如此，它使用了Google的V8虚拟机（Google的Chrome浏览器使用的JavaScript执行环境），来解释和执行JavaScript代码。
除此之外，伴随着Node.js的还有许多有用的模块，它们可以简化很多重复的劳作，比如向终端输出字符串。
因此，Node.js事实上既是一个运行时环境，同时又是一个库。

>要使用Node.js,首先需要进行安装。关于如何安装Node.js，这里就不赘述了，可以直接参考官方的安装指南。安装完成后，继续回来阅读下面的内容。`引自`[nodebeginner](http://www.nodebeginner.org/index-zh-cn.html)


1.	如何通过NODE把在服务器端编写的JS代码运行起来。
        客户端：通过浏览器渲染HTML的时候，渲染HTML页面引入的JS。
        服务器端：
        1，,webstrom中js文件右键run即可相当于node服务环境中运行
        2，NODE命令：在执行的目录下shift+鼠标右键，输入node+js文件名，即可运行。
        3，cmd中输入node，然后写代码测试。
2. 常用的cmd命令：
	除了在win运行框中输入cmd外，我们通常使用`shift+鼠标右键` ，选择 在此打开命令提示符 来运行cmd
	`mkdir xxx`  创建一个文件夹,`rd xxx` 删除一个文件夹，
	`echo >1.txt `创建文件，`del xxx` 删除指定文件
	`cd xxx`  `cd..`进入某个文件夹、返回上一级，
3. >NODEJS是由很多的模块组成的一个供JS运行和调取特殊方法实现相关功能的一个环境的运行平台。

 Nodejs: Javascript + 网络库和网络IO（异步IO） + 事件机制（EventLoop) + 原生组件机制（Addons ）+包机制（ npm）
        ->内置模块。：只要安装了node，天生自带的模块有：例如http\fs\url:
        ->第三方模块：别人写好和封装的优秀模块，如果我们同样想实现类似的功能，只需要把对应的模块安装到NODE环境平台上即可。npmjs.com 是npm包管理平台。

4. 在NODEJS中，JS修改一次，一般情况下，想让最新的代码起到效果，我们需要重新的使用NODE执行一次。
在NODEJS中，全局对象不是window，而是global；
> 前端路由：根据前端不同的请求，服务器端接受到客户端的请求后，根据客户端请求资源文件的（html，css，js，jpg，img，audio，video）的不同，服务器端进行不同的处理，

5. 一个基本的node服务器模型：
执行此代码后，打开浏览器输入`localhost:8888/index.html`或者`你的ip + :8888/index.html`即可看到Hello World.
```
var http = require("http");//引入模块（http是内置模块，稍后会详解）
var server=http.createServer(function(request, response) {
  console.log("Request received.");
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.write("Hello World");
  response.end();
})
server.listen(8888);
```

### 以下是node自带的核心模块详解：

1. 一些不在模块中的常用的方法：

如何计算代码的执行时间
```
console.time('start');
for(var i = 0; i<1000000000;i++){
}
console.timeEnd('start');
```
node中执行文件的大致顺序 
	正常的同步代码 > nextTick > setTimeout(0) > setImmediate > io
```
```
setImmediate(function () {
    console.log('setImmediate1');
});
setTimeout(function () {
    console.log('setTimeout');
},0);
process.nextTick(function () {
    console.log('nextTick');
});
/*for(;true;){
    console.log(100);
}*/
console.log(1999);
```
process上的方法：
```
process.cwd()	//current working directory 当前工作目录
process.chdir('..');change dir //改变目录(当前的工作目录是可以更改的 当前文件夹是不能更改)
process.stdin.on('data', function (data) {//监听用户输入,标准输入,参数data就是我们监听到的数据
    console.log(data.toString());
});
process.stdout.write('hello');//向控制台内输出 console.log调用的就是这个方法
process.memoryUsage()); //监听内存的使用量,检测内存泄漏(rss 常住内存,heapTotal使用的总量,heapUsed 堆的使用量)
```
require详解：
```
var a  = require('./a.js');//加载其他模块，//多次加载同一个模块，第一次会将其缓存下来，第二次加载走缓存，发现缓存过了，就不再继续加载了

//删除require缓存的方法：
require.resolve('./a.js');//解析出一个绝对路径，根据文件名
//1.我们要通过绝对路径找到对应的属性，进行删除；
//2.去缓存内找到对应的缓存模块require.cache
//3.找到对应的模块require.cache[require.resolve('./a.js')]
//4.delete
delete require.cache[require.resolve('./a.js')];
```
第三方模块导出接口方式exports和module.exports的原理:
```
(function (exports,require,module,__dirname,__filename) {
    //模块化 返回对象 暴露接口
    //__dirname __filename 是通过外界传进来的，所以可以在文件中访问
    exports = module.exports = {};
    //exports  = Person;//改变了exports指向但是，module没有变化
    function Person (){}
    //方法1：给exports增加属性会影响到module.exports值
    //错误方式epxorts = Function
    //exports.person = Person 
    //方法2：直接改变module.exports
     module.exports = {person:Person};//引用地址
    return module.exports;
    //如果导出的是引用数据类型 用module.exports =Function
})();
```
2. util模块：

> util模块是一个Node.js 核心模块，提供常用函数的集合，用于弥补核心JavaScript 的功能 过于精简的不足。主要功能有：

判断类型
```
console.log(util.isArray([]));
console.log(util.isError(new Error()));
console.log(util.isRegExp(/^$/));
console.log(util.isDate(new Date()));
```
深度解析
util.inspect是一个将任意对象转换 为字符串的方法，通常用于调试和错误输出。它至少接受一个参数 object，即要转换的对象。
//showHidden 是一个可选参数，如果值为 true，将会输出更多隐藏信息。
//depth 表示最大递归的层数，如果对象很复杂，你可以指定层数以控制输出信息的多 少。如果不指定depth，默认会递归2层，指定为 null 表示将不限递归层数完整遍历对象。 如果color 值为 true，输出格式将会以ANSI 颜色编码，通常用于在终端显示更漂亮 的效果。
```
util.inspect(object,[showHidden],[depth],[colors])

```
继承父类的方法：util.inherits是一个实现对象间原型继承 的函数。
JavaScript 没有 提供对象继承的语言级别特性，而是通过原型复制来实现的。
```
util.inherits(Child,Parent);
```
3. buffer详解：
>JavaScript 语言自身只有字符串数据类型，没有二进制数据类型。
但在处理像TCP流或文件流时，必须使用到二进制数据。因此在 Node.js中，定义了一个 Buffer 类，该类用来创建一个专门存放二进制数据的缓存区。

//buffer是16进制，每逢16进1.大于256的对256取模，负值是256加负值，其他的不在范围或者字符串什么的就是00.
```
 var buffer = new Buffer([17,18,19]); 
buffer.toString('utf8',3,6)//toStirng的参数('编码',开始,结尾)//与字符串的转换
//另外，node还自带一个bufferi解码模块string_decorder
var String_decoder = require('string_decoder').StringDecoder;
var sd = new String_decoder; //向当于实例一个对象
console.log(sd.write(buffer.slice(0,4)).toString());
console.log(sd.write(buffer.slice(4)).toString());
//都往sd对象里写 写第一次的时候，发现不能组成汉字的留下来，写第二次的时候在拼接上。

Buffer.concat(); //合并buffer
a.copy(b,3);//拷贝，把a拷贝到b中，默认是从原buffer的开始考到结束

```
封装buffer中的concat方法:
```
Buffer.myConcat = function (list,totalLength) {//把buffer拼成一个  buffer.copy考入到大buffer中之后返回
    //先判断当前list的个数，如果只有一个直接返回
    if(list.length==1){return list[0];}
    //我们要判断totalLength有没有传递
    if(typeof totalLength=='undefined'){
        totalLength  = 0;
        list.forEach(function (item) { //遍历数组拿到每一项的长度
            totalLength+=item.length;//计算出总长度
        });
    }
    var buffer = new Buffer(totalLength); //要把每一个小的buffer copy到大buffer里面
    var index = 0;
    list.forEach(function (item) {
        item.copy(buffer,index);//将每一个item放入到buffer中
        index+= item.length;
    });
    return buffer.slice(0,index);//防止第二个参数totalLength过长，我们通过自己维护的索引进行截取
};
```
5. 最后详解base64问题：
> base64是一段编码，不用三次握手，四次分手，不用请求，有利于优化。主要步骤为：
1，16进制转10 ，然后转2，Base64编码可用于在HTTP环境下传递较长的标识信息。、码的数据不会被人用肉眼所直接看到。“防君子不防小人”
2,2进制连到一起，每隔6位分割开，前面补2个0，
3，在转成10进制。在64位字符串中拿到。
我们通过转换一个‘米’字来简单看一下base64的转换原理：
```
var buffer=new Buffer('米');
console.log(buffer);//e7 b1 b3
console.log(parseInt('e7',16));//步骤1：16进制转10 ，然后转2，Base64编码可用于在HTTP环境下传递较长的标识信息。、码的数据不会被人用肉眼所直接看到。“防君子不防小人”
console.log(parseInt('b1',16));
console.log(parseInt('b3',16));

console.log((231).toString(2));
console.log((177).toString(2));
console.log((179).toString(2));//11100111 10110001 10110011//步骤2：2进制连到一起，每隔6位分割开，前面补2个0，

console.log(parseInt('00111001',2));//再转成10进制。在64位字符串中拿到。
console.log(parseInt('00111011',2));
console.log(parseInt('00000110',2));
console.log(parseInt('00110011',2));

//57 59 7 35
var str='ABCDEFGHIJKLMNOPQRSTUVWXYZ'
        +'abcedfghigklmnopqrstuvwxyz'
        +'0123456789'
        +'+/';
console.log('str.length: '+str.length);
var res=str[57]+str[59]+str[6]+str[51];
console.log(res);
console.log(new Buffer(res,'base64').toString());//最后转会汉字查看是否转换成功。
```
