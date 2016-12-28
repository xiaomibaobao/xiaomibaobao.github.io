# Express
>+ Express是一个简洁、灵活的`node.js` Web应用开发框架, 它提供一系列强大的功能，比如：`模板解析`、`静态文件服务`、`中间件`、`路由控制`等等,并且还可以使用插件或整合其他模块来帮助你创建各种 Web和移动设备应用,是目前最流行的基于Node.js的Web开发框架，并且支持Ejs、jade等多种模板，可以`快速地搭建`一个具有完整功能的网站。
>+ Express 不对 Node.js 已有的特性进行二次抽象，只是在它之上扩展了 Web 应用所需的基本功能。
### 1.一个简单的 Express 路由
```
var express = require('express');
var app = express();

// 没有挂载路径的中间件，应用的每个请求都会执行该中间件
app.use(function (req, res, next) {
  console.log('Time:', Date.now());
  next();
});

// 挂载至 /user/:id 的中间件，任何指向 /user/:id 的请求都会执行它
app.use('/user/:id', function (req, res, next) {
  console.log('Request Type:', req.method);
  next();
});//第一个参数path为请求的路径,第二个参数以及以后的参数为处理请求的回调函数，参数为请求和响应对象

// 路由和句柄函数(中间件系统)，处理指向 /user/:id 的 GET 请求，使用app.all可以处理所有的在同一URL的各种HTTP请求。
app.get('/user/:id', function (req, res, next) {
  res.send('USER')//如果由send和end向客户端发送请求，则后面的同名路由永远不会被执行。
});
app.get('/user/:id', function (req, res, next) {
  res.send('1')
});
app.use(function(err, req, res, next) {//错误处理中间件，四个参数必须保证都在形参，即使不需要 next 对象，也必须在形参中声明它，否则中间件会被识别为一个常规中间件，不能处理错误。一般有next（err）调用。
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```
### 2中间件 以及常用方法
##### 1.1中间件
是处理HTTP请求的函数，用来完成各种特定的任务 比如检查用户是否登录、检测用户是否有权限访问等，它的特点是:

>+ 一个中间件处理完请求和响应可以把相应数据再传递给下一个中间件
>+ 作为中间件系统的路由句柄，使得为路径定义多个路由成为可能。
>+ 回调函数的next参数,表示接受其他中间件的调用，函数体中的next(),表示将请求数据传递给下一个中间件
>+ 还可以根据路径来区分进行返回执行不同的中间件
>具体例子见上文app.use

##### 1.2 获取参数和查询字符串和Params
```
app.get('/',function(req,res){
   res.end('欢迎来到首页'+req.host+" "+req.path);//req.host 返回请求头里取的主机名(不包含端口号),req.path 返回请求的URL的路径名
   //http://localhost:3000/?a=1&b=2&c=3
   res.send(req.query);
   res.send(req.params.id+" "+req.params.name);//params可以用来获取请求URL中的参数值
});
```
##### 1.3 send end
>+ end 只能传递string或者Buffer，包括JSON字符串。 
>+ send方法向浏览器发送一个响应信息，并可以智能处理不同类型的数据 并在输出响应时会自动进行一些设置，比如HEAD信息、HTTP缓存支持等等。
> 1. 当参数为一个String时，Content-Type默认设置为"text/html"。
> 2. 当参数为Array或Object时，Express会返回一个JSON
> 3. 当参数为一个Number时，并且没有上面提到的任何一条在响应体里，Express会帮你设置一个响应体，比如：200会返回字符"OK"

##### 1.4 简单封装中间件
```
var url = require('url');
var app = function(req,res){//监听函数 请求和响应
    var method = req.method.toLowerCase();//获取请求的方法
    var urlObj = url.parse(req.url,true);
    var pathname = urlObj.pathname;//请求的路径
    //获取主机名
    req.hostname = req.headers['host'].split(':')[0];
    //得到路径名
    req.path = pathname;
    //取得查询字符串对象
    req.query = urlObj.query;
    var index =0;//定义当前迭代的路由索引
    function next(err){
        if(index>=app.router.length){
            return res.end('Not Found');
        }
        var route = app.router[index++];//取出当前的路由配置项
        var routeMethod = route.method;//取出路由配置项方法名
        if(err){
            //交由错误处理中间件来处理
            if(routeMethod == 'all' && route.fn.length == 4){
                route.fn(err,req,res,next);
            }else{//如果不是中间件或者 不是错误 处理中间件
                next(err);//跳过正常的中间件和路由
            }
        }else{
            if(routeMethod == 'all'){//表示中间件配置
                if(pathname == route.path || route.path == '*'){//如果请求路径和当前路由配置的路径相同
                    route.fn(req,res,next);//执行此中间件函数
                }else{
                    next();
                }
            }else{
                if(pathname == route.path || route.path == '*'){
                    route.fn(req,res);
                }else{
                    next();
                }
            }
        }
    }
    next();
}

app.router = [];//里面存放着所有的路由配置项 {method,path,fn}
function express(){
    return app;
};

//循环所有的方法，给app增加这些方法名同名的属性
['get','post','put','delete','option','patch'].forEach(function(method){
    app[method] = function(path,fn){
        //请求的方法 请求的路径 和监听回调函数
        app.router.push({method:method,path:path,fn:fn});
    }
});
//配置所有的方法,相同的路径返回相同内容
app.all = function(path,fn){
    ['get','post','put','delete','option','patch'].forEach(function(method){//增加路由配置
        app.router.push({method:method,path:path,fn:fn});
    });
}

app.use = function(path,fn){
    //如果只传了中间件函数，没有传路径
    if(typeof path =='function'){
        fn = path;
        path = '*';
    }
    app.router.push({method:'all',path:path,fn:fn});
}

app.listen = function(port){
    require('http').createServer(app).listen(port);
}

module.exports = express;
```
### 2 模板
在nodejs中使用express框架，它默认的是ejs和jade渲染模板
##### 2.1使用模板
以ejs为例子：
```
app.set('view engine', 'ejs');//指定渲染模板文件的后缀名为ejs
app.set('views',path.join(__dirname,'views'));
//app.engine( '.html', require( 'ejs' ).__express ); 模板使用其他后缀的情况//__express是ejs模块的一个公共属性，表示要渲染的文件扩展名
res.render(view, [locals], callback);//参数view就是模板的文件名，在渲染模板时locals可为其模板传入变量值，callback用来处理返回的渲染后的字符串
```
##### 2.2 模板原理
```
var tmpl = '<h1>{{name}}</h1><h1>{{age}}</h1>';
var data = {name:'xiaomi',age:23};
var html= tmpl.replace(/\{\{(\w+)\}\}/g,function(input,group){
    return data[group];
})
console.log(html);
```
##### 2.3 静态文件服务器（也叫静态中间路由）
express.static 是 Express 唯一内置的中间件。它基于 serve-static，负责在 Express 应用中提托管静态资源。
访问静态资源文件时，express.static 中间件会根据目录添加的顺序查找所需的文件。
下面的例子使用了 express.static 中间件，其中的 options 对象经过了精心的设计。
```
var options = {
  dotfiles: 'ignore',
  etag: false,
  extensions: ['htm', 'html'],
  index: false,
  maxAge: '1d',
  redirect: false,
  setHeaders: function (res, path, stat) {
    res.set('x-timestamp', Date.now());
  }
}

app.use(express.static('public', options));
//如果没有options：
app.use(express.static(path.join(__dirname,'public')));
app.get('/hello',function(req,res){
    res.send('hello');
});
app.listen(9090);
```
##### 2.4 简单封装static
```
function static(root){
  return function(req,res,next){
      //1. 先去判断一下对应的路径里有没有此文件，有读返回，没有跳过
      // /index.html
      var filename = path.join(root,req.path);
      fs.exists(filename,function(exists){
         res.setHeader('Content-Type',mime.lookup(filename));
        if(exists){
            fs.createReadStream(filename).pipe(res);
        }else{
            next();
        }
      })
  }
}
```

### 3redirect 重定向
##### 3.1 简单封装redirect
```
app.use(function(req,res,next){
    res.redirect = function(url){
        //如何控制客户端向新的服务url发请求
        res.statusCode = 302;//控制客户端重定向
        res.setHeader('Location',url);//设置重定向的URL地址
        res.end('');
    }
    next();
});
```
##### 3.2 redirect实践
当用户登陆时，根据post请求，判断用户是否用户名和密码输入正确并返回相应页面：
```
app.post('/login',function(req,res){
   var user = req.body;
   if(user.username == 'admin' && user.password == 'admin'){
        res.render('user',user);
   }else{
       res.redirect('back');
   }
});
```


```