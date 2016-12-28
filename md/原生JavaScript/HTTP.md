# HTTP
>+ http:超文本传输协议，除了可以传递文本之外，对于图片，音频，视频，html等等都可以传输。
    客户端和服务端数据或者内容传输，靠的就是HTTP。
>+ URI 统一资源标识符，URL 统一资源定位符，是URI的一种，还有一种叫URN，
 URL：协议，域名，端口号
    在两个URL地址中，以上三点，有一个不一样就属于跨域.


###七层协议　以及 TCP/IP 协议：
- 物理层：网卡
- 链路层：网卡驱动
- 网络层：ip
- 传输层：tcp udp协议
- 会话层：保持会话，断开会话（此层由系统维护）
- 表示层：加密解密
- 应用层：http ftp  https
>+ TCP/IP 协议是传输层协议，主要解决数据如何在网络中传输，而HTTP是应用层协议，主要解决如何包装数据。
tcp一次链接中的三次握手，四次挥手看的有点晕，以后说。


#http事务
http是一个事务：事务指包含N+1个事件，事件全部完成后，事物才算完成，否则失败。
                http先有request，后有response，其内容称为报文，包括三部分
- 起始行（基本信息）
                        request起始行：http方法、协议\url、http_version；
                        response起始行：http方法（和request相同）、状态码（http_status)，状态码描述符。
- 首部（摘要信息）、格式固定，键值对。
                        `·请求首部`（cookies，`响应首部`（date，`通用首部`（content-type，`实体首部`，`扩展首部`即自定义。
- 主体（数据）

 #### http传输方式 http method：

**get**：向服务器获取数据（给的少，拿的多）url以及html中所有的href和src。
        **head**：只获取服务器响应报文中的起始行和首部，比如获取服务器时间，想知道下载文件有多大。
        **delete** 向服务器删除数据。成功返回202，其他都是200
            **特点**：  
1.  把参数放到url后面。
2. 没有请求主体，
3. url有大小限制，8k 2k 7k
4. 容易被缓存（节省带宽），  优化时有好处，把静态页面都缓存， （？_=Math.random()    //Date () get Date();）可以用来处理缓存。
5. 明文发送，不安全。
**post：**向服务器发送数据
**put：**向服务器增加数据；推送。成功返回202，其他都是200
**特点**
1. 有请求主体，没有大小限制，
2. 不会被缓存，  
3. 不会明文发送，相对安全                                
> 此外还有一个  `options：`检验当前服务器支持哪些http方法，               
         不管哪一种传输方式，既可以向服务器传递数据，也可以从服务器获取数据。只不过每一种传输方式都有自己的特殊用途。
             restful:表征状态转移（服务器接口和软件架构，服务器拿到请求，就知道要怎么办）

**请求的状态码**：xhr.readyState
0: (Uninitialized) the send( ) method has not yet been invoked.
1: (Loading) the send( ) method has been invoked, request in progress.
2: (Loaded) the send( ) method has completed, entire response received.
3: (Interactive) the response is being parsed.
4: (Completed) the response has been parsed, is ready for harvesting.

响应的http状态码：
  1xx：http事务正在初始化，在http2.0中把1xx都删掉了，只保留101，我们目前大部分都是http1.1；
>  http1.1和http2.0的区别：
                    1，多路复用，1.1会有三次握手，四次挥手，性能差，2.0中多个TCP请求复用一个TCP。
                    2，首部压缩：响应报文更小。
                    3，服务器端推送：
           ` websocket`（f12,-network-WS），html5提供，用来网页即时聊天。此时会有101，表示切换协议。从https切换到ws协议。
            
**2xx**：客户端请求成功
           ` 200`，ok
           ` 202`，接受，服务器请求已经接受，用在delete方法和put方法，
**3xx**：缓存或者重定向
          `  301 `，永久转移，a域名永久转移到b域名，站点之间跳转，会降低搜索引擎的权重 seo。
           ` 302`，临时转移，可以站点之间跳转，也可以站点内跳转。登陆的时候会用到（a.login.html-->a.index/html)，服务器端转移。
           ` 303`，重定向。
        >   ` 304` 缓存， 涉及到两个响应首部，`last-modified`（请求的文件最后一次修改的时间：GMT格式，精确到秒，不准确）,`etag,（`把请求的静态资源通过 摘要算法 计算得到的散列值，得到字符串）两个请求首部，if-modified-since , if-none-match,和etag对应
**强缓存**：涉及到两个响应首部。
`expirese，`静态资源过期时间，浏览器端的GMT时间，不准。绝对时间：`Expires`表示存在时间，允许客户端在这个时间之前不去检查（发请求），等同max-age的效果。但是如果同时存在，则被`Cache-Control`的max-age覆盖。 cache-control，缓存多少秒。相对请求的时间往后推多少秒。
通过HTTP的META设置expires和cache-control:
`<meta http-equiv="Cache-Control" content="max-age=7200" />
<meta http-equiv="Expires" content="Mon, 20 Jul 2009 23:00:00 GMT" />`

 **4xx：**客户端错误
            400：错误的请求，少参数，url长度超过限制，请求的端口错误
            401：未认知：没有登陆或者登陆信息无效
            403：禁止访问
            404：资源不存在
        **5xx：**服务器错误
             500，服务器遇到了位置错误。
             503，请求并发人数过多，服务器停止处理后续请求。
#### http scheme：
>`http`：80，
           ` https，`比http传输更加安全的协议，默认端口443，https=http+SSL/STL,加密方式：非对称加密 RSA算法（质数）。
                        两者区别：1，https需要`ca证书`，2，`端口`不同，3,`链接方式`不同，http是`tcp协议`，https是`SSL加密`、有身份认证的网络协议。4，http不走`第六层。`
                        SSL：1，,`认证`用户和服务器，2，`加密传输`，防止窃取和保证数据完整。
            ftp，文件（非文本）上传协议，默认端口21
            smtp：邮件传输协议，端口25
            file：不走网络，无法请求ajax，是打开本地文件预览。

#### location对象
在控制台输入`location`来获得location对象：相关参数可以拿到当前网络服务器的相关参数：url的组成部分，
            location是一个`对象`： 包含当前url的信息。类似NODE端url.parse方法中的属性。（`pathname` ,`hash`，`hostname/host/port`，`search`，`protocol，`, `href，`）（
            `方法`有：
            - `assign（）加载新文档`，
            -`eload` 载文档
            - `replace` 替换
    
#### hash的作用
>+ **单页应用**都是通过hash来实现的，hash是不会发送到服务器的， 所以就不会刷新页面一些前端框架通过这个来实现。 url中#后面的就是hash。使用#定位`不会刷新页面`，也不会向服务端发送请求。但是会改变访问历史。
>+ **锚点定位：**　为网页位置指定标识符，有两个方法。一是    使用锚点，比如`<a name="print"></a>，`二是使用id属性，比如`<div id="print">。`
>+ **不利于搜索引擎发现**，seo优化，”#！“;如果你希望Ajax生成的内容被浏览引擎读取，那么URL中可以使用"#!"，Google会自动将其后面的内容转成查询字符串_escaped_fragment_的值。
　　            比如，Google发现新版twitter的URL：`http://twitter.com/#!/username`
　　            就会自动抓取另一个URL：`http://twitter.com/?_escaped_fragment_=/username`
>+ **onhashchange**事件
　　            这是一个HTML 5新增的事件，当#值发生变化时，就会触发这个事件。IE8+它的使用方法有三种：
                       ` window.onhashchange = func;
                        <body onhashchange="func();">
                        window.addEventListener("hashchange"， func, false);`

### 本地存储，页面之间的通信
>+  url传参，从A跳到B，我们把A页面一些操作信息通过地址栏传参的方式传递给B，让B可以获取到A页面的操作信息。
            其中#只能在末尾。
 如果传递来的参数值有中文或者其他符号，一般都会编码。encodeURIComponent
获取URL参数的方法1 ：字符串的截取和拼接     
   
```
    var str = "http://kbs.sports.qq.com/kbsweb/game.htm?mid=100000:1468573&name=kbs#nba";
   // ->这个就是B页面地址栏中的地址,我们需要获取的是{mid:"100000:1468573",name:"kbs"}
        var strIndex = str.indexOf("?"),
                endIndex = str.lastIndexOf("#"),
                resStr = "";
        if (strIndex > -1) {
            if (endIndex === -1) {
                resStr = str.substring(strIndex + 1);
            } else {
                resStr = str.substring(strIndex + 1, endIndex);
            }
        }
        var ary = resStr.split("&"),
                obj = {};
        for (var i = 0; i < ary.length; i++) {
            var cur = ary[i],
                    curAry = cur.split("=");
            obj[curAry[0]] = curAry[1];
        }
        console.log(obj);
```                
>方法2：
```
    //->正则处理
    var obj = {};
    str.replace(/([^?=&#]+)=([^?=&#]+)/g, function () {
        obj[arguments[1]] = arguments[2];
    });
    console.log(obj);
```
>+ IFRAME，嵌套，我们可以在A页面中把B页面嵌套进来，然后利用一些IFRAME提供的方法获取B的内容， 目前这个方法正在逐步减少。常用在CMS,CRM系统。它地无刷新机制需要AB页面同域，特别经常用在表单上传的无刷新提交，所以后台使用较多。可以使用模块化，SEAJS等，实现页面固定模块和移动模块。
            缺点，不利于SEO优化解析，并且无法操作iframe中的document；
             iframe是inline-block元素，在生成的时候，framborder，scrolling，src几乎都要指定。


##### 本地存储，用户访问某个页面，把一些信息存储到客户端浏览器中（本地）
 1. **cookies：**
- cookie存储是有大小限制的，一般情况下，浏览器规定，每一个源下的cookie只能最高存储4KB。
- 而且在同源http（非https）是明文发送的。安全性，  无论是cookie还localStorage都是明文存储，（在控制台可以看到)所以对于重要的数据尽量不要存储到本地，如果非要存储请进行加密。
- 会附加在每一个http请求的request，response的header中都是要被传输的。
- 过期时间，我们设置cookies的时候可以指定，（有些时候会失效，比如用清理软件清理的时候）
- 户可能出于安全角度禁用cookies，（隐身模式，无痕模式）
-  移除cookies只需要把时间设置成此时以前的即可。

 2. **HTML5中新增的API（webStorage）**；在IE7、IE6中可以使用`IE User Data`实现。
- 虽然localStorage存储在本地，但不同的浏览器存储存储数据是独立的，所以在Chrome上存储的localStorage在FireFox上是获取不到的。
- localStorage和sessionStorage只能存储字符串类型，对于复杂的对象可以使用ECMAScript提供的JSON对象的stringify和parse来处理，低版本IE可以使用json2.js。
- localStorage(ie678 不兼容);持久计划存储到本地，不管页面是否关闭。一般情况下一个源允许最多存储5MB内容。
- 没有过期时间，除非人为删除（js、控制台）
                      `  localStorage.setItem、 localStorage.getItem、 localStorage.removeItem、 localStorage.clear（）、可以用escape，unescape转码解码。`
- sessionStorage：会话存储，（当前页面关闭就不行了。）
- 如果是密码，一般网站都使用MD5不可逆转加密。（hex_md5(123456)）;

