# Gulp
>` gulpjs1`是一个前端构建工具，与`gruntjs`相比，gulpjs无需写一大堆繁杂的配置参数，API也非常简单，学习起来很容易，而且gulpjs使用的是nodejs中`stream来读取`和操作数据，其速度更快。gulp相对grunt来说更容易上手，并且有很高的开发效率。
>+ 易于使用 通过代码优于配置的策略，Gulp 让简单的任务简单，复杂的任务可管理。
>+ 快速构建 利用 node.js 流的威力，你可以快速构建项目并减少频繁的 IO 操作。前一级的输出，直接变成后一级的输入，使得在操作上非常简单
>+ 高质量的插件 Gulp 严格的插件指南确保插件如你期望的那样简洁地工作。
>+ 易于学习 通过最少的 API，掌握 gulp 毫不费力，构建工作尽在掌握。

#### 流（Stream）以及gulp中的流：
**NODE中的‘流’：**
>+ Stream是nodejs各种对象实现的抽象接口。
>+ 所有的stream对象都是EventEmitter的实例,可以发射事件。
>+ 流是一种有起点和终点的数据传输手段。
>+ 上一个的输出是下一个的输入

**gulp中的流：**
>+ gulp正是通过代码优于配置的策略来尽量简化任务编写的工作。
>+ 类似jquery里的链式操作，把各个方法串连起来构建完整的任务。
>+ 用gulp编写任务也可看作是用Node.js代码编写任务。
>+ 当使用流时,gulp不需要生成大量的中间文件，只将最后的输出写入磁盘，整个过程因此变得非常快。

### gulp安装
gulp安装和其他npm包不太一样，在安装了node环境后，在大多数情况下需要两步步：[为什么?](http://stackoverflow.com/questions/22115400/why-do-we-need-to-install-gulp-globally-and-locally)
1. `npm install -g gulp`全局安装gulp。
2. - 还需要在每个要使用gulp的项目中都单独安装一次。
- 如果想在安装的时候把gulp写进项目package.json文件的依赖中，需要：`npm install --save-dev gulp`
#### 开始gulp
和Grunt一样， gulp需要讲其代码放在`gulpfile.js`中，然后用require引入`var gulp = require('gulp');`运行gulp的`目录`应该是这个样子的：
├── gulpfile.js
├── node_modules
│ └── gulp
└── package.json
下面是一个最简单的gulpfile.js文件**内容示例**，它定义了一个默认的任务:
```
var gulp = require('gulp');
gulp.task('default',function(){
    console.log('hello world');
});
```
**运行gulp：**
在`gulpfile.js`的目录下用cmd运行`gulp`+`我们定义好的gulp中的任务名`即可；如果没有指定任务名，则会执行任务名为`default`·的默认任务。

**gulp.js工作方式** 
>+ 首先通过gulp.src()方法获取到想要处理的文件流
>+ 然后把文件流通过pipe方法导入到gulp的插件中
>+ 最后把经过插件处理后的流再通过pipe方法导入到gulp.dest()中
```
gulp.dest()方法则把流中的内容写入到文件中
    var gulp = require('gulp');
    gulp.src('script/src.js')             // 获取文件的流的api
        .pipe(gulp.dest('dist/dest.js')); // 写文件的api
```

## Gulp的四个核心API
#### 1 gulp.src()
在gulp的工作方式里，我们知道，gulp是依赖`流`工作的，`gulp.src()`方法正是用来获取流的.
但要注意这个流里的内容不是原始的文件流,而是一个`虚拟文件对象流,`这个虚拟文件对象中存储着原始文件的路径、文件名和内容等信息
`gulp.src(globs[, options])`
+ globs 参数是文件匹配模式(类似正则表达式)，用来匹配文件路径(包括文件名)，当然这里也可以直接指定某个具体的文件路径。当有多个匹配模式时，该参数可以为一个数组
+ options 为可选参数。通常情况下我们不需要用到
>+ Gulp内部使用了node-glob模块来实现其文件匹配功能。`globs `参数详解，引用自[无双的博客](http://www.cnblogs.com/2050/p/4198792.html%22%E6%97%A0%E5%8F%8C%E7%9A%84%E5%8D%9A%E5%AE%A2%22)
* 匹配文件路径中的0个或多个字符，但不会匹配路径分隔符，除非路径分隔符出现在末尾
`** `匹配路径中的0个或多个目录及其子目录,需要单独出现，即它左右不能有其他东西了。如果出现在末尾，也能匹配文件。
`?` 匹配文件路径中的一个字符(不会匹配路径分隔符)
`[...] `匹配方括号中出现的字符中的任意一个，当方括号中第一个字符为^或!时，则表示不匹配方括号中出现的其他字符中的任意一个，类似js正则表达式中的用法
`!(pattern|pattern|pattern)` 匹配任何与括号中给定的任一模式都不匹配的
`?(pattern|pattern|pattern) `匹配括号中给定的任一模式0次或1次，类似于js正则中的(pattern|pattern|pattern)?
`+(pattern|pattern|pattern) `匹配括号中给定的任一模式至少1次，类似于js正则中的(pattern|pattern|pattern)+
`*(pattern|pattern|pattern) `匹配括号中给定的任一模式0次或多次，类似于js正则中的(pattern|pattern|pattern)*
`@(pattern|pattern|pattern) `匹配括号中给定的任一模式1次，类似于js正则中的(pattern|pattern|pattern)
`gulp.src(['js/*.js','css/*.css','*.html'])`当有多种匹配模式时可以使用数组

#### 2 gulp.dest()
`gulp.dest(path[,options])`
gulp的使用`流程`一般是这样子的：首先通过`gulp.src()`方法获取到我们想要处理的文件流，然后把文件流通过pipe方法导入到`gulp的插件`中，最后把经过插件处理后的流再通过pipe方法导入到`gulp.dest()`中，gulp.dest()方法则把流中的内容`写入`到文件中，这里首先需要弄清楚的一点是，我们给gulp.dest()传入的路径参数，只能用来指定要生成的文件的目录，而不能指定生成文件的文件名，要想改变文件名，可以使用插件`gulp-rename`,这个等下会提到。
```
var gulp = require('gulp');
gulp.src('script/jquery.js')
    .pipe(gulp.dest('dist/foo.js'));
//最终生成的文件路径为 dist/foo.js/jquery.js,而不是dist/foo.js
```
一般情况下我们gulp.dest`(path)`生成的文件路径是我们传入的path参数后面再加上gulp.src()中有`通配符(**)`开始出现的那部分路径。如果我们想灵活应用，通过指定gulp.src()方法配置参数中的`base`属性，我们可以更灵活的来改变gulp.dest()生成的文件路径。
```
gulp.task('copy',function(){
    gulp.src(['./app/*/*.css'
            ,'./app/img/**/*.png'
            ,'./app/js/*.js'
            ,'!./app/js/tmp.js'],{base:'./app'})//
        .pipe(gulp.dest('./dist'))
});
gulp.task('default',['copy']);
//再如：所以改变base路径后，gulp.dest()生成的文件路径也会改变
gulp.src(script/lib/*.js) //没有配置base参数，此时默认的base路径为script/lib
    //假设匹配到的文件为script/lib/jquery.js
    .pipe(gulp.dest('build')) //生成的文件路径为 build/jquery.js

gulp.src(script/lib/*.js, {base:'script'}) //配置了base参数，此时base路径为script
    //假设匹配到的文件为script/lib/jquery.js
    .pipe(gulp.dest('build')) //此时生成的文件路径为 build/lib/jquery.js  
```

#### 3 gulp.task()
`gulp.task`方法用来定义任务，`gulp.task(name[, deps], fn)`
```
gulp.task('mytask', ['array', 'of', 'task', 'names'], function() { //定义一个有依赖的任务,当前定义的任务[第二个参数]会在所有依赖的任务执行完毕后才开始执行。如果没有依赖，则可省略这个参数。
  // Do something
});
```
注意了，gulp并不会等待那个所依赖的异步任务完成，而是会接着执行后续的任务。
```
gulp.task('first',function(){
  //one是一个异步执行的任务
  setTimeout(function(){
    console.log('one is done')
  },5000);
});

//two任务虽然依赖于one任务,但并不会等到one任务中的异步操作完成后再执行,紧接着执行two任务。所以two任务会在one任务中的异步操作完成之前就执行了。
gulp.task('second',['first'],function(){
  console.log('two is done');
});
//如果想要second等待first，可以使用以下方法：
gulp.task('one',function(cb){ //cb为任务函数提供的回调，用来通知任务已经完成
  //one是一个异步执行的任务
  setTimeout(function(){
    console.log('one is done');
    cb();  //执行回调，表示这个异步任务已经完成
  },5000);
});
gulp.task('two',['one'],function(){//这时two任务会在one任务中的异步操作完成后再执行
  console.log('two is done');
});
方法二：定义任务时返回一个流对象。适用于任务就是操作gulp.src获取到的流的情况。
gulp.task('one',function(cb){
  var stream = gulp.src('client/**/*.js')
      .pipe(dosomething()) //dosomething()中有某些异步操作
      .pipe(gulp.dest('build'));
    return stream;
});

gulp.task('two',['one'],function(){
  console.log('two is done');
});

第三：返回一个promise对象，我还不太懂promise。。。。
```

#### 4 gulp.watch()
```
gulp.task('default',function(){
    gulp.watch('./app/index.html',function(event){// event事件对象:type added changed deleted// path 变化的文件的路径
            console.log(event);
    });
});
```

###一些常用的gulp插件
 **使用插件步骤 #**
- npm install xxx --save-dev 安装插件
- 在 gulpfile.js 顶部引入此插件
- 在创建任务的时候使用此插件
如果我们要使用的插件非常多，我们可以在gulpfile.js中使用`gulp-load-plugins`来帮我们加载插件,然后我们要使用`gulp-concat`和`gulp-connect`这两个插件的时候， 就可以使用`$.concat和$.connect`来代替了,注意$后面的`'  .'`别忘了写。也就是原始插件名去掉gulp-前缀，之后再转换为`驼峰命名。`
```
var gulp = require('gulp');
//加载gulp-load-plugins插件，并马上运行它
var plugins = require('gulp-load-plugins')();
```
> 编译 Sass：gulp-sass
编译 Less：gulp-less
合并文件：gulp-concat
压缩js 文件：gulp-uglify
重命名js文件：gulp-rename
优化图像大小：gulp-imagemin:在保证不改变图像质量的情况下，让图像文件的体积变得更小一点
压缩css 文件：gulp-minify-css

```
   gulp.task('minify',function(){
        return gulp.src('app/less/page.less')//指定 less文件
            .pipe(less())//把less编译成css
            .pipe(gulp.dest('dist/css'))//输出到目的地
            .pipe(minify())//对 css再进行压缩
            .pipe(rename('page.min.css'))//重命名
            .pipe(gulp.dest('dist/css'));//输出到目的地
    });

```

创建本地服务器：gulp-connect
实时预览 gulp-connect:有些时候我们需要把文件放到本地服务器上去预览，gulp-connect可以帮我们创建一个本地服务器去运行我们的项目
代码检查 jshint 注意必须同时安装jshint和gulp-jshint

###### 小项目实战，自动刷新 


```
var gulp=require('gulp');
var $=require('gulp-load-plugins')();
gulp.task('copy',function(){
    gulp.src('./app/index.html')
        .pipe(gulp.dest('./dist'))
        .pipe($.connect.reload());
});
gulp.task('watch',function(){
    gulp.watch('./app/index.html',['copy'])
});
gulp.task('server',['watch'],function(){
    $.connect.server({
        root:'./dist',
        port:'8888',
        livereload:'true'
    })
});
```