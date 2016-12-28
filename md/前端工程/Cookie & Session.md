# Cookie & Session

> cookie是为了辩别用户身份，进行会话跟踪而存储在客户端上的数据
> cookie使用注意事项
- 可能被客户端篡改，使用前验证合法性
- 不要存储敏感数据，比如用户密码，账户余额
- 使用httpOnly保证安全
- 尽量减少cookie的体积
- 设置正确的domain和path，减少数据传输
###　1.Cookie的处理流程
<img src="/img/cookie.jpg" />
###　2.向客户端发送cookie
>+ node原生：`res.setHeader('Set-Cookie',['name=xiaomi','age=1']);`
>+ express：`res.cookie(name,value,[,options]);`
options详细说明：
|参数|chrome对应属性|类型|说明|示例|
|:-------|:-------|:-------|:-------|:-------|
|domain|Domain|String|域名，默认是当前域名|{domain:'a.zfpx.cn'}|
|path|Path|String|路径，默认是/|{path:'/visit'}|
|expires|Expires|Date|过期时间，如果没以有指定或为0表示当前会话有效|{expires:new Date(Date.now()+20*1000)}|
|maxAge|Max-Age|Number|有效时间(单位是毫秒)|{maxAge:20*1000}|
|httpOnly|HTTP|Boolean|不能通过浏览器javascript访问|{httpOnly:true}|
|secure|Secure|String|只通过https协议访问| |
### 3. 获取cookie
##### 3.1 使用cookie-parser中间件来设置和获取cookie
```javascript
$ npm install cookie-parser --save
```
```javascript
app.use(require('cookie-parser')());    //使用中间件
response.cookie(key,value)              //在响应中向客户端设置cookie
request.cookies                         //获取请求中的cookie对象
response.clearCookie('username')        //清除cookie  
```
##### 3.2 cookieParser原理解析
```javascript
function cookieParser(req, res, next){
    if (!req.headers.cookie) {
        return next();
    }
    req.cookies =  require('querystring').parse(req.headers.cookie,'; ','=');
    res.cookie = cookie;
    next();
}

function cookie(name, val, options) {
    var opt = options || {};
    var value = encodeURIComponent(val);
    var pairs = [name + '=' + value];
    if (null != opt.maxAge) {
        var maxAge = opt.maxAge - 0;
        if (isNaN(maxAge)) throw new Error('maxAge should be a Number');
        pairs.push('Max-Age=' + Math.floor(maxAge));
    }

    if (opt.domain) {
        pairs.push('Domain=' + opt.domain);
    }

    if (opt.path) {
        pairs.push('Path=' + opt.path);
    }

    if (opt.expires) pairs.push('Expires=' + opt.expires.toUTCString());
    if (opt.httpOnly) pairs.push('HttpOnly');
    if (opt.secure) pairs.push('Secure');

    return pairs.join('; ');
}
```

##### 3.3 记录客户端的访问次数
```javascript
var express = require('express');
var cookieParser = require('cookie-parser');
var app = express();
/**
 * 如果要加密的话 cookieParser里要指定密码，而且signed要等于true
 */
app.use(cookieParser('zfpx'));
app.get('/write',function(req,res){
    res.cookie('age','123',{signed:true});//防止客户端篡改cookie
    res.end('ok');
});

app.get('/read',function(req,res){
    console.log(req.signedCookies);//读取正确的的cookies
    res.send(req.cookies);
});

//记录这是客户端的第几次访问
app.get('/visit',function(req,res){
    res.cookie('count',isNaN(req.cookies.count)?0:parseInt(req.cookies.count)+1);
    res.send(req.cookies);
});
app.listen(9090);
```
##### 3.4记住密码页面的简单实现
```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        function setCookie(key,value){
            document.cookie = key+"="+value;
        }
        function getCookie(key){
            if(document.cookie){
                var parts = document.cookie.split('; ');
                for(var i=0;i<parts.length;i++){
                    var vals = parts[i].split('=');
                    if(vals[0]==key){
                        return vals[1];
                    }
                }
                return "";
            }else{
                return "";
            }
        }
        function checkCookie(){
            var username = getCookie('username');
            if(username){
                alert('欢迎'+username+'再次访问');
                document.querySelector('#username').value = username;
            }else{
                username = prompt('请输入用户名:');
                if(username){
                    setCookie('username',username);
                }
            }
        }
    </script>
</head>
<body onLoad="checkCookie()">
<input type="text" id="username">
</body>
</html>
```

### 4 加密
`var crypto=require('crypto');`
##### 4.1常用的加密算法：
crypto.createHash('sha1').update('1').digest('hex');
crypto.createHash('sha256').update('1').digest('hex');
crypto.createHash('md5').update('1').digest('hex');
crypto.getHashes('sha1').update('1').digest('hex');

>**HMAC算法**HMAC算法将散列算法与一个密钥结合在一起，以阻止对签名完整性的破坏 生成私钥.
```
$ openssl genrsa -out key.pem 1024
var crypto = require('crypto');
var shasum = crypto.createHmac('sha1', 'xiaomi');//根据自己设定的密钥设置，可以防止反推。
var result = shasum.update('hello').digest('hex');//digest指定编码，比如hex，base64
console.log(result);
```

##### 4.2 MD5的原理：
>+ md5 把任意长度的输入，变成固定长度的输出。用来完整性校验,加密密码等。
>+ 不同的输入永远产生不同的输出，相同输入会永远相同输出。
>+  不能从输出反推输入，计算不可逆。

##### 4.3 加密cookie
```
var crypto = require('crypto');
var sign = function(val, secret){
    return val + '.' + crypto
            .createHmac('sha256', secret)
            .update(val)
            .digest('base64')
            .replace(/\=+$/, '');
};
console.log(sign('123','xiaomi'));
```
### 5 Session
#####5.1 什么是session
session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而session保存在服务器上

客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上，这就是session。客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了

#####5.2 cookie与session区别

1. cookie数据存放在客户的浏览器上，session数据放在服务器上。
2. cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗 考虑到安全应当使用session
3. session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能 考虑到减轻服务器性能方面,应当使用COOKIE
4. 单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie
> 将登陆信息等重要信息存放为session、其他信息如果需要保留，可以放在cookie中

#####5.3 session实现
1. 在服务器端生成全局唯一标识符`session_id`
2. 在服务器内存里开辟此`session_id`对应的数据存储空间
3. 将`session_id`作为全局唯一标示符通过`cookie`发送给客户端
4. 以后客户端再次访问服务器时会把`session_id`通过请求头中的`cookie`发送给服务器
5. 服务器再通过`session_id`把此标识符在服务器端的数据取出

```javascript
var express = require('express');
var cookieParser = require('cookie-parser');
var app = express();
app.use(cookieParser());
//存放会话数据 key卡号 value就是卡号对应的数据对象
var sessions = {};
//与客户端约定的会话ID
var SESSION_KEY = 'session.id'
//当用户访问根目录的时候 执行对应的回调函数
app.get('/',function(req,res){
    res.setHeader('Content-Type','text/html;charset=utf-8');
   // 1. 先取出cookie中的sessionId 卡号
    var sessionId = req.cookies[SESSION_KEY];
    // 如果有卡号的，也就是有ID的话 老顾客
    if(sessionId){
        //取出此卡号对应的信息，余额
        var sessionObj = sessions[sessionId];
        if(sessionObj){
            //扣掉10块钱
            sessionObj.balance = sessionObj.balance -10;
            res.send('欢迎你老顾客，你卡上还剩'+sessionObj.balance);
        }else{
            genId(res);
        }
    //如果没有的话就是新顾客
    }else{
        genId(res);
    }
    function genId(res){
        //由店家生成一个唯一的卡号
        var id = Date.now()+''+Math.random();
        //要在店家的小本上记录一下此卡号对应的余额
        sessions[id] = {balance:100};
        //把这个卡发给顾客带回家
        res.cookie(SESSION_KEY,id);
        //告诉 用户送他一张卡
        res.send('欢迎你新顾客，送你一张价值100元的剪发卡');
    }
});

app.listen(9090);
```

#####5.4 session中间件
```javascript
$ npm install express-session
```
|参数|描述|
|:-----|:-----|
|name|设置 cookie 中，保存 session 的字段名称，默认为 connect.sid|
|store|session 的存储方式，默认存放在内存中，也可以使用 redis，mongodb 等|
|secret|通过设置的 secret 字符串，来计算 hash 值并放在 cookie 中，使产生的 signedCookie 防篡改|
|cookie|设置存放 session id 的 cookie 的相关选项，默认为 (default: { path: '/', httpOnly: true, secure: false, maxAge: null })|
|genid|产生一个新的 session_id 时，所使用的函数， 默认使用 uid2 这个 npm 包|
|rolling|每个请求都重新设置一个 cookie，默认为 false|
|resave|即使 session 没有被修改，也保存 session 值，默认为 true|

#####5.5 小案例： session实现权限
```javascript
var express = require('express');
var cookieParser = require('cookie-parser');
var session = require('express-session');
var uuid = require('uuid');
var app = express();
app.set('view engine', 'html');
app.engine('html', require('ejs').__express);
app.set('views', __dirname);
app.use(require('cookie-parser')());

app.use(session({secret: 'zfpx',
    resave: true,
    saveUninitialized: true}));

/**
 * curl -v -H "cookie: username=customer" http://localhost:8080/user
 */

function checkUser(req, res, next) {
    if (req.session && req.session.username)
        next();
    else
        res.redirect('/');
}

//进入登录页
app.get('/', function (req, res) {
    res.render('index');
});

//登录
app.get('/login', function (req, res) {
    req.session.username = req.query.username;
    res.redirect('/user');
});

//用户页面
app.get('/user', function (req, res) {
    console.log(req.session);
    res.render('user', {username: req.session.username});
});

//用户退出
app.get('/logout', function (req, res) {
    req.session.usrename = null;
    res.redirect('/');
});


app.listen(8080);

```

#####5.6 存储到mongodb数据库
session数据都是存储在内存当中的，当进程退出后，session数据就会丢失
我们可以把session存储到mongodb数据库当中
```
$ npm install connect-mongo
```

```javascript
var MongoStore = require('connect-mongo')(session);
//解析 cookie 请求头中的cookie转成对象 req.cookies
app.use(cookieParser());
// req.session
app.use(session({
  secret:'zfpx',//加密cookie的密钥
  resave:true,//重新保存
  saveUninitialized:true,//保存未初始化的session
  store:new MongoStore({// 指定会话的数据库存储位置
    url:'mongodb://123.57.143.189:27017/zhufengnodejs'
  })
}));
```