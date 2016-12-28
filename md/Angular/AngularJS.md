#  AngularJS

### 什么是 AngularJS？

>+ AngularJS 使得开发现代的单一页面应用程序（SPAs：Single Page Applications）变得更加容易。
>+ AngularJS 把应用程序数据绑定到 HTML 元素。
>+ AngularJS 可以克隆和重复 HTML 元素。
>+ AngularJS 可以隐藏和显示 HTML 元素。
>+ AngularJS 可以在 HTML 元素"背后"添加代码。
>+ AngularJS 支持输入验证。

### AngularJS 或MVC应用组成如下：
View(视图), 即 HTML。
Model(模型), 当前视图中可用的数据。
Controller(控制器), 即 JavaScript 函数，可以添加或修改属性。
####为什么需要MVC
- 代码规模越来越大，切分责职
- 相同逻辑代码要复用
- 需求变更需要重构
- 职责清晰，代码模块化
- MVC只是手段，目标是模块化和复用
#### 前端MVC困难
1. 操作DOM的代码必须等待整个页面加载完成
2. 多个JS出现互相依赖，程序员自己解决
3. JS的原型继承给前端编程带来很多困难。利用原型继承来模拟继承效果。

### AngularJS 表达式

+ AngularJS 表达式可以包含字母，操作符，变量。AngularJS 表达式支持过滤器。可以写在 HTML 中。不支持条件判断，循环及异常。
+ AngularJS 表达式写在双大括号内：{{ expression }}。
+ AngularJS 表达式把数据绑定到 HTML，这与  指令有异曲同工之妙。
+ AngularJS 表达式 很像 JavaScript 表达式：它们可以包含文字、运算符和变量。

### AngularJS Scope(作用域)
>+ 引入angular js后，会得到一个angular对象，
>+ 一切从模块开始，angular.module('名字'，[依赖的模块，可以使用空数组]，[config])，返回值是当前模块。
>+  `app.run(function($rootScope)){}` ,run方法在加载完所有模块后会被执行一次，在启动时最先执行，一般用来配置默认属性，$templateCache.put('xx.html',<div>)可以用来缓存模板。在指令中用get调用。
>+ 当你在 AngularJS 创建控制器时，你可以将` $scope` 对象当作一个参数传递:`$scope`就是MVVM中的VM。
>+ 当在控制器中添加 `$scope `对象时，视图 (HTML) 可以获取了这些属性。
>+ 视图中，你不需要添加 `$scope` 前缀, 只需要添加属性名即可，如： {{carname}}。必须在同一`ng-app`作用域下。
>+ 根作用域,所有的应用都有一个` $rootScope`，它可以作用在 ng-app 指令包含的所有 HTML 元素中。`$rootScope `可作用于整个应用中。是各个 controller 中 scope 的桥梁。用 rootscope 定义的值，可以在各个 controller 中使用。
>+ `$scope and $rootScope`1. 继承：子可以继承父，反之不行。2. 调用：
	
### AngularJS 模块
1. 模块定义了一个应用程序。模块是应用控制器的容器。控制器通常属于一个模块。
2. 对于 HTML 应用程序，通常建议把所有的脚本都放置在 <body> 元素的最底部。
3. 这会提高网页加载速度，因为 HTML 加载不受制于脚本加载。
4. 对 angular.module 的调用只能在库加载完成后才能进行。有时候会决定script的位置。
5. 模块间的依赖：
```
 var app = angular.module('appModule',['appModule1']);
让appModule去依赖appModule1
    //先依赖 之后appModule模块加载的时候把appModule1的模块的同名控制器覆盖掉了
    //先加载依赖模块，先查找依赖的模块，先加载依赖，在执行自己模块
```

6. 启动多个模块
````
  //通过bootstrap方法启动两个模块,第二个参数是启动的模块名字并且是数组的形式,html标签上不写ng-app=“”
    angular.bootstrap(div1,['a1']);
    angular.bootstrap(div2,['a2']);
$scope.data = [
            {name:'你好',value:'你好'},
            {name:'我好',value:'我好'},
            {name:'大家好',value:'大家好'}
        ];
        //根据select的value值默认被选中
        $scope.select = 3;
```
7.` ng-model-options `
### AngularJS 控制器
需要通过模块来声明控制器
1. controller和DOM平行。，
2. 控制会可以声明变量和方法
3. 控制器嵌套，会有继承关系。
4. 注意：
        1，不要复用controller，
        2，不要操作DOM
        3，不要在控制器中格式化数据，用filter，
        4，一般Controller之间不要相互调用，控制器之间的交互通过事件进行。（scope或者事件）
        不然会改掉数据(控制器中拿到的一般都是最新数据)。
5. **压缩时会变，要使用数组写法**.
6. 控制器之间的交互：
```
$scope.$on('all', function (event,data) {
            //data代表儿子传递过来的总价格
            $scope.total = data
        });
        //监听模型的名字
        $scope.$watch('total', function (newVal,oldVal) {
            //向下发布告诉子控制器
            $scope.$broadcast('money',newVal);
        });

$scope.computed = function () {
            var total = $scope.product.price* $scope.product.count;
            console.log(total);
            //通知给父亲 发射事件 $emit;
            //事情的名字 事情的内容
            //告诉当前的事件是计算all  all的值是total
            $scope.$emit('all',total);
        };
```
### AngularJS 绑定
` ng-init`，在当前作用域下初始化数据。可以用；隔开，设置多个初始,通常初始化数据都在controller中。
` ng-bind`用法等同于{{}} 好处在于不会出现闪烁
`ng-bind-template` 绑定多组模板。仍然可以解决闪烁问题 `ng-bind-template:'{{name}} {{age}}'`
`ng-non-bindable` 取消绑定;
`ng-cloak`，防止多重绑定闪烁，原理先让元素display：none 数据获取后display：block
` ng-switch`根据我们的数据显示不同的代码块
 
```js
 <input type="text" ng-model="name">
<div ng-switch="name">
    <div ng-switch-when="hello">你好</div>
    <div ng-switch-when="world">世界</div>
    <div ng-switch-default>hello world</div>
</div>
```
`$sce:`
```
$sce $scope.sth=$sec.trustAsHtml('<h1>sss</h1>')
```
### ng-repeat，遍历数据,
>+  数据相同时无法遍历，可以使用`‘track by $index’ `来遍历对象(特别是数组等)，只要地址不相同即可。
``` 
<div ng-init="phones=[{name:'iphone',price:40,type:['white',black]},{name:'xiaomi',price:4,type:['orange','blue']},{name:'moto',price:60,type:['yellow','pink']}]">
    <div ng-repeat="(index,p) in phones">
        <div ng-repeat="pp in p.type">
            {{index}}-{{$index}}-{{pp}}
        </div>
    </div>
</div>
<div ng-init="phones=[{name:'a'},{name:'a'},{name:'a'}]">
    <div ng-repeat="(index,p) in phones track by index">
        {{p}}
    </div>
</div>

```
>+ **AngularJS Select(选择框)**    AngularJS 可以使用数组或对象创建一个下拉列表选项
 `ng-repeat` 指令是通过数组来循环 HTML 代码来创建下拉列表，但 **ng-options** 指令更适合创建下拉列表，它有以下优势：
**使用 ng-options 的选项的一个对象， 当选择值是一个对象时，我们就可以获取更多信息，应用也更灵活。**
```
ng-repeat 是一个字符串。
在哪里遍历遍历出来的东西,d in data
d.value as一个作为value值
d.name for为了用户可见的值
-->
<select ng-model="select" ng-options="d.value as d.name for d in data">
</select>
```

### AngularJS 过滤器 

>+ **货币**过滤器：{{200| currency:'￡'}}后面的参数表示钱的种类
>+ **大小写**过滤：uppercase ，lowercase
>+  **时间戳**：（由13位组成）日期过滤器，data：yyyy-MM-dd hh-mm-ss,需要13位时间戳。
>+  限制**长度**，LimitTo，截取多少位。
>+ **数字**过滤器：number：n，如果不写N，默认千进制格式化，N代表保留多少位小数,小数这边默认保留三位可以指定。。
>+  **json**：注意放在pre标签中。{obj | json }
>+  **orderBy** 过滤器根据表达式排列数组：
`<tr ng-repeat="c in course | orderBy:active:flag">`
```
<body ng-init="course=[{name:'student1',chinese:70,math:20},{name:'student12',chinese:30,math:10},{name:'student3',chinese:90,math:80}]">
<div ng-init="flag=true;active='chinese'">
    <table class="table table-bordered">
        <tr>
            <td>名字</td>
            <td ng-click="flag=!flag;active='chinese'">国语分</td>
            <td ng-click="flag=!flag;active='math'">数学分</td>
        </tr>
        <tr ng-repeat="c in course | orderBy:active:flag">//参数1.根据具体的字段排序，字符串类型 //参数2.true为倒序 false为升序
            <td>{{c.name}}</td>
            <td>{{c.chinese}}</td>
            <td>{{c.math}}</td>
        </tr>
    </table>
</div>
</body>
```
>+ **自定义过滤器**：需要在控制器中过滤
```
 app.filter('capitalize',function($scope){//自定义过滤器中还可以再使用 定义好的过滤器
       return function (input,len){
           return input.slice(0,len).toUpperCase()+input.slice(len);
       }
    });
    app.controller('ctrl',function($scope,capitalizeFilter){//如何在控制器中使用。
        $scope.name='abcd';
        $scope.name=capitalizeFilter($scope.str,1);//参数第二个是{abcd | 1}对照上面的过滤器；
    })
```

### 一些常见事件和方法
>+ :ng-href 、ng-src就是防止点击为空 要等待数据过来后再渲染图片 防止控制台404;
>+  `$scope.$apply();`用来更新最新的值。
>+  **watch方法**

```
//每当我们想监控的值变化时，我们要监听他的新值和旧值
        //$watch 监听的是模型上的方法。模型的名字
        //写法1：
        /*$scope.$watch("name", function (newVal,oldVal) {
            console.log(newVal,oldVal);
        });*/
        //写法2： 返回作用域的改变的值
        $scope.$watch(function () {
            return $scope.name;
        }, function (newVal,oldVal) {
            console.log(newVal,oldVal);
        });
```
>+  **change方法**
```
       ` $scope.change ` = function () {
            console.log($scope.name);
        }
      `  $scope.copy` = function () {
            alert('我用了copy键');
        }
       ` $scope.paste` = function () {
            alert('我用了粘贴');
        }
        `$scope.cut `= function () {
            alert('我用了剪切');
        }
```
 >+  `$timeout `服务对应了 JS window.setTimeout 函数。`$interval `服务对应了 JS window.setInterval 函数。`$timeou.cancel()`取消定时器
### AngularJS HTML DOM
>+ ng-disabled 指令直接绑定应用程序数据到 HTML 的 disabled 属性。
>+ ng-show /ng-hide 只是简单的操作css样式；ng-show 指令根据 value 的值来显示（隐藏）HTML 元素。
>+ ng-if如果条件不成立删除元素，并且内部指令不执行；
       **需要频繁切换用show**；第一次能确定下来（确定数据是否存在），**不频繁切换的用ng-if**，频繁切换样式不是很消耗性能，但是东西已经定下来了，初始的时候消耗较大。工作常用ng-repeat配合ng-if；
        

>+  增加class值：
>特点：和原生样式没有冲突。
1.  写法1 
    ` <div ng-class ="{true:'red',false:'yellow'}[isActive]"></div>// [isActive]为true时，赋值red；`
    -({true:'red',false:'yellow'})[true],JS原生写法。
2.  写法2 `<div ng-class ="{'selected':isSelected}"></div>//此时isSelected为true就会给class赋值selected；`
3.  写法:行内样式    `   <div ng-style ="{color:'red'}"></div>； `
4.  写法4 ：`<div class ="{{name}}"></div>`

### AngularJS 原生自带方法。
>+ angular.element将原生内容变成jq对象，操作DOM的时候必须指定单位。
>+ angular.equals
>+ copy：是浅拷贝，深拷贝复制一份到新的地址和新的指针，浅拷贝，只有一个指针。
>+ angular.fromJson(JSON.parse)&&angular.toJson(JSON.stringify)
>+ forEach
>+ bind

### AngularJS 输入验证 
> 客户端的验证不能确保用户输入数据的安全，所以服务端的数据验证也是必须的。
>+ HTML 表单属性 novalidate 用于禁用浏览器默认的验证。比如HTML5自带的。
>+ 使用ng-pattern="/PATTERN/"来确保输入能够匹配指定的正则
`<input type="text" ng-pattern="/[a-zA-Z]/" /> `
用angular操作dom的时候， ng-show指令，css在属性是是` $dirty 或 $invalid `才显示。
`<span style="color:red" ng-show="myForm.email.$dirty && myForm.email.$invalid">`
1. `$dirty`表单有填写记录
2.`$pristine`表单没有填写记录
3. `$invalid`字段内容非法
4. `$valid` 字段内容合法
>+ 为应用程序数据提供类型验证（number、email、required）。某个表单输入是否已填写，只要在输入字段元素上添加HTML5标记required即可。
```
<form ng-app="" name="myForm">
    Email:
    <input type="email" name="myAddress" ng-model="text">
    <span ng-show="myForm.myAddress.$error.email">不是一个合法的邮箱地址</span>
</form>
<p>在
```

### AngularJS 包含
>+ 客户端包含
通过 JavaScript 有很多种方式可以在 HTML 中包含 HTML 文件。
通常我们使用 http 请求 (AJAX) 从服务端获取数据，返回的数据我们可以通过 使用 innerHTML 写入到 HTML 元素中。

>+ 服务端包含
大多服务端脚本都支持包含文件功能 (SSI： Server Side Includes)。
使用 SSI, 你可在 HTML 中包含 HTML 文件，并发送到客户端浏览器。

>+ AngularJS 包含
使用 AngularJS, 你可以使用 ng-include 指令来包含 HTML 内容:
 `<div ng-include="'myUsers_List.htm'"></div>`
  `<div ng-include="'myUsers_Form.htm'"></div>`
   ` ng.view : 一个html只能用一次，`
  
### AngularJS 动画
>+ AngularJS 提供了动画效果，可以配合 CSS 使用。
>+ AngularJS 使用动画需要引入 angular-animate.min.js 库。还需在应用中使用模型 ngAnimate：
<body ng-app="ngAnimate">
>+ ngAnimate 做了什么?
- ngAnimate 模型可以添加或移除 class 。
- ngAnimate 模型并不能使 HTML 元素产生动画，但是 ngAnimate 会监测事件，类似隐藏显示 HTML 元素 ，如果事件发生 ngAnimate 就会使用预定义的 class 来设置 HTML 元素的动画。
AngularJS 添加/移除 class 的指令:
`ng-show  ng-hide  ng-class  ng-view  ng-include  ng-repeat  ng-if  ng-switch`
- ng-show 和 ng-hide 指令用于添加或移除 ng-hide class 的值。
- 其他指令会在进入 DOM 会添加 ng-enter 类，移除 DOM 会添加 ng-leave 属性。
- 当 HTML 元素位置改变时，ng-repeat 指令同样可以添加 ng-move 类 。