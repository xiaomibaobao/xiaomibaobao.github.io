# AngularJS 2
### RESTful
一种前后端API设计模式，representive state transfer
>+ 每一个URL代表一个资源，
>+ 资源呈现的**具体形式**叫做他的**表现**。
>+ 不再根据路径来判断，而是根据一个路由下，不同参数和get post delete put来操作。实现表现层状态转换.

### directive 指令
>+ 使用 `.directive` 函数来添加自定义的指令。分为两种，装饰型和组 件式的。
>+ 要调用自定义指令，HTML 元素上需要添加自定义指令名。
>+ 使用驼峰法来命名一个指令，` runoobDirective,` 但在使用它时需要以 - 分割, `runoob-directive:`
>+ compile函数用来对模板自身进行转换，而link函数负责在模型和视图之间进行动态关联。
>+ Agular中会把多个控制器通用的部分放在service中，而而不是一个公共的控制器中。angular不推荐这样做。 
>+ 
```
//指令可以装饰我们的页面也可以创造标签
    //ng开头的指令是angular自带的指令，我们写指令的时候，不要用ng开头
    //一切从模块开始
    var app = angular.module('appModule',[]);
    //我们的指令是`不依赖`于`控制器`的
    //通过模块声明指令
    //param1：指令的名字
    //param2: 是函数
    app.directive('hello', function () {
        //默认返回一个对象
        return {
            restrict:'EACM',//ECMA//限制指令使用范围默认为EA,class样式默认不会替换，注释内容被插入后仍然看不到一般我们的指令只写在EA上（element，attribute）
            replace:true,//只保留模板内容原有的指令被替换掉
            transclude:true,将自定义标签中的内容保留，
            template:'<div>angular baby,React MM</div>',//模板,将hello标签里的内容变为<div>Hello</div>
            //传递参数时，要通过对象传递 键代表的是名字：
            template:'{{name}}{{myage}}<div ng-click="add({person:name,personage:myage})">angular MM</div>'
            scope{//只要写了scope，则会让指令上的scope独立起来，不会相互影响。
            title:@/&/=;//绑定策略：&符用来引用指令上的方法。//@符是不支持双向数据绑定 取到的只是字符串,//=支持双向数据绑定，引用的是当前作用域下对应的变量
            }
           compile: function (elements,attrs) {//参数可变//elements代表当前指令元素//attrs Attribute
                var times = attrs['time']; //获取编译次数
                var tmpl = elements.children();//操作指令模板,先获取默认的模板<div>Hello zfpx</div>
                //根据次数生成要追加的模板
                //因为以前已经有一个模板了，只需要追加time-1个
                for(var i =0; i<times-1;i++){
                    //append具有移动性，所以我们要克隆出来
                    elements.append(tmpl.clone());
                }
                return function () {
                    console.log('link');
                }
            },//当我们写了compile函数后 link函数，就不用自己在写link函数
            link: function (scope,element,attrs) {  //链接函数
                element.html(123);
                //scope代表当前作用域
                //element代表当前的元素 element为jq元素 angular.element
                //attrs代表当前指令上的所有属性
                //属性的名字用-的话也需要转换成驼峰命名
                console.log(attrs['myText']);
            }
        }
    });
```
>+ 指令之间的交互：
```
 app.directive('lovemoney', function () {
        //当前的lovemoney指令是依赖于girl的
        return {
            restrict:'A',
            //因为当前的lovemoney和lovecry就在当前的指令上
            require:'^girl',
          /*  require:'girl',//直接写girl表示在当前指令下查找（平级）
            require:'^girl'//直接写girl表示在当前指令下查找（平级+上一级）
            require:'?^girl',//找不到的话不报错
            require:'?girl',//找不到的话不报错*/
            //当我们依赖了girl指令就会默认把girl的控制的实例注入进来
            link: function (scope,ememnts,attsr,girlCtrl) {
                //girlCtrl是girl的controller的实例，在girl控制器上定义的this上的方法或者属性，都可以通过girlCtrl调用到
                girlCtrl.add('lovemoney');
            }
        }
    })
```
### ui-router
>+ ng的路由和ajax相比：
- ajax请求不会留下History记录，在后台系统没关系，但是用户无法保存书签或者共享- 好友。但是通过hash值就不一样了。
- ajax无法SEO。
- html5 的History API，可以实现不刷新但是留下历史记录


