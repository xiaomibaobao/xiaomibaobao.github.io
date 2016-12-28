# ES6
>ES6作为Javascript的下一代标准，是js进军编写复杂的大型应用程序，成为企业级开发语言的关键。此外：ECMAScript和JavaScript的关系是，前者是后者的规格，后者是前者的一种实现（另外的ECMAScript方言还有Jscript和ActionScript）。在日常场合，这两个词是可以互换的。
###  1. 作用域变量
>以前的ES5中，只有全局作用域和私有作用域（函数作用域），那么使用var定义变量会带来两个问题：
- 在当前作用域定义后，在当前作用域中都可以访问，如果变量名重复，则后者覆盖前者，所以及其容易导致变量污染。
- 在for循环中的i（常用的）在循环内外都可以访问到，也是由于没有块级作用域造成的。
##### 1.1 let
> ES6中引入了块级作用域的概念，用了let，不仅仅可以通过闭包隔离，还增加了一些块级作用域隔离。由此带来：
- 不会污染全局变量
- for循环中如果有循环嵌套都使用i的话也不会互相影响。
- 重复由let定义变量会报错，在同一块儿级作用域中。
- 由let定义的变量不再有预解释机制。
- 闭包的新写法：
```
//之前：
（function(){}）（）
//现在
{
}
```

##### 1.2 常量const
使用const我们可以去声明一个常量，常量一旦赋值就不能再修改了，注意const限制的是不能给变量重新赋值，而变量的值本身是可以改变的,下面的操作是可以的。
```
const names = ['xiaomi'];
names.push('xiaoming');
console.log(names);
```
不同的块级作用域可以多次定义 ,并互不影响。
```
const A = "0";
{
    const A = "A";
    console.log(A)
}
{
    const A = "B";
    console.log(A)
}
console.log(A)
```
实战一下：新手很容易写出类似的代码。
```
<button>一</button>
<button>二</button>
<button>三</button>
<button>四</button>

<div id="output"></div>

<script>
  var buttons = document.querySelectorAll('button')
  var output = document.querySelector('#output')

  for (var i = 0; i < buttons.length; i++) {
    buttons[i].addEventListener('click', function() {
      output.innerText = buttons[i].innerText
    })
  }
</script>
```
通过排查，我们可以发现，每次我们点击按钮时，`事件监听回调函数`中得到的变量 i 都会等于 buttons.length，也就是这里的 4。而 buttons[4] 恰恰不存在，所以导致了错误的发生。在ES6中，我们可以吧for循环的语句中的var 改成 let；因为 let 语句会使该变量处于一个 ` 块级作用域 ` 中，从而让事件监听回调函数中的变量引用得到保持。

### 3. 解构
##### 3.1 解析数组
解构意思就是分解一个东西的结构,可以用一种类似数组的方式定义N个变量，可以将一个数组中的值按照规则赋值过去。
```
var [name,age] = ['xiaomi',23];
console.log(name,age);
```
##### 3.2 嵌套赋值
```
let [x, [y], z] = [1, [2.1, 2.2]];
console.log(x, y, z);
let [x, [y,z]] = [1, [2.1, 2.2]];
console.log(x,y,z);
```
##### 3.3 省略赋值
```
let [, , x] = [1, 2, 3];
console.log(x);
```
##### 3.2 解构对象
```
var obj = {name:'xiaomi',age:8};
//对象里的name属性的值会交给name这个变量，age的值会交给age这个变量
var {name,age} = obj;
//对象里的name属性的值会交给myname这个变量，age的值会交给myage这个变量
let {name: myname, age: myage} = obj;
console.log(name,age,myname,myage);
```
在赋值和传参的时候可以使用默认值
```
let [a = "a", b = "b", c = throw new Error('C必须指定')] = [1, , 3];
console.log(a, b, c);

function ajax (options) {
    var method = options.method || "get";
    var data = options.data || {};
    //.....
}
function ajax ({method = "get", data}) {
    console.log(arguments);
}
ajax({
    method: "post",
    data: {"name": "zfpx"}
});
```
### 4. 字符串
##### 4.1模板字符串 
```
var name='xiaomi';
    var age='18';

function show(strs,...values){//可以在模板字符串的前面添加一个标签，这个标签可以去处理模板字符串,标签其实就是一个函数,函数可以接收两个参数,一个是strings,就是模板字符串里的每个部分的字符,还有一个参数可以使用rest的形式values,这个参数里面是模板字符串里的值.
    console.log(strs);
    console.log(values);
}

let all=show`${name}今年${age}岁了`;//模板字符串用反引号(数字1左边的那个键)包含，其中的变量用${}括起来
```
##### 4.2字符串新方法 
```
var strs='xiaomi';
console.log(strs.startsWith('a',2));//后半段,如果是一个参数，则表示是否是以此参数开始。
console.log(strs.endsWith('o',4));//判断，前半段
console.log(strs.includes('o',3));//后半段

console.log('repeat below');
console.log('x'.repeat(0),'y'.repeat(10));
```

###5  函数
##### 5.1 指定默认的参数值
```
let desc  = function(name='xiaomi',age=8){//如果不指定函数的参数的值，就会使用参数的这些默认的值	
    console.log(`${name} is ${age} years old!`);
}
desc('xiaomi');//xiaomi is 8 years old
```
##### 5.2 展开操作符
```
//传入参数
let print = function(a,b,c){
    console.log(a,b,c);
}
print([1,2,3]);//[1, 2, 3]
print(...[1,2,3]);//1,2,3

// 可以替代apply
var m1 = Math.max.apply(null, [8, 9, 4, 1]);
var m2 = Math.max(...[8, 9, 4, 1]);

// 可以替代concat
var arr1 = [1, 3];
var arr2 = [3, 5];
var arr3 = arr1.concat(arr2);
var arr4 = [...arr1, ...arr2];//[1,3,3,5]
console.log(arr3,arr4);

//类数组的转数组
function max(a,b,c) {
    console.log(Math.max(...arguments));
}
max(1, 3, 4);
```
##### 5.3 剩余操作符
```
let rest = function(a,...b){
    console.log(a,b);
}
rest(1,2,3);//1,[2,3]
```
##### 5.3 函数的名字以及解构
```
var desc = function descname(){}
console.log(desc.name);//descname

let destruct = function({name,age}){
    console.log(name,age);
}
destruct({name:'xiaomi',age:6});//xiaomi 6
```
##### 5.4 箭头函数
>+ 箭头函数简化了函数的的定义方式，一般以 "=>" 操作符左边为输入的参数，而右边则是进行的操作以及返回的值inputs=>output
>+ 输入参数如果多于一个要用()包起来，函数体如果有多条语句需要用{}包起来.
>+ 箭头函数根本没有自己的this，导致内部的this就是外层代码块的this。 正是因为它没有this，从而避免了this指向的问题。
```
       setTimeout(function(){console.log(this);},1000); //在浏览器执行的话this指向window
       setTimeout()() => console.log(this),1000);//在浏览器执行的话this指向person
```
##### 5.5一些新方法
```
//from
let newArr = Array.from(oldArr); //from将一个数组或者类数组变成数组,会复制一份.
//of
console.log(Array(4), Array(3, 4).length,Array.of(3, 4), Array.of(3, 4).length);//of是为了将一组数值,转换为数组
//copyWithin
console.log([1,2,3,4,5].copyWithin(1,3,4));//,参数一开始的位置，参数二开始的下标，参数三，结束的下标，包前不包后
//findindex、find
[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
}) // 10，如果是findindex则返回其索引。
//fill
['a', 'b', 'c'].fill(7)// [7, 7, 7]，如果有原数组，会更改原数组。
new Array(3).fill(7)// [7, 7, 7]
```

### 6. 对象
6.1 对象字面量
```
let name = 'xiaomi';
let age = 8;
let getName = function(){
    console.log(this.name);
}
let person = {
    name,
    age,
    getName
}
person.getName();//在对象里添加跟变量名一样的属性，
```
对象的一些新方法：
```
//Object.is
console.log(Object.is(NaN,NaN));//true ,特殊情况。
// Object.assign 
var nameObj = {name:'xiaomi'};
var ageObj = {age:8};
var obj = {};
Object.assign(obj,nameObj,ageObj);
console.log(obj);//Object {name: "xiaomi", age: 8}
function clone (obj) {//克隆对象
  return Object.assign({}, obj);
}
// Object.setPrototypeOf;super 
var obj1={name:'xiaomi'};
var obj2={name:'xiaoming'};
var newguy={};
Object.setPrototypeOf(newguy,obj2);
console.log(newguy.name);//xiaoming
let person ={
    eat(){
        return 'milk';
    }
};
let student = {
    __proto__:person,//可以直接设置 __proto__
    eat(){
        return super.eat()+' bread'//通过super可以调用prototype上的属性或方法,通过 __proto__得到的。
    }
};
console.log(student.eat());
```

### Symbol 
>+ 表示独一无二的值 它是JavaScript语言的第七种数据类型.
>+ 在对象的内部，使用Symbol值定义属性时，Symbol值必须放在方括号之中
`var person = {[luckNum]: '8'};`
>+ 由于每一个Symbol值都是不相等的，这意味着Symbol值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性,可以消除魔术变量。
```
var operator={
    add:Symbol(),
    minus:Symbol()//add 和minus永远不会重复了。
};
function cal(op,a,b){
    switch (op){
        case 'add':
            return a+b;
        break;
        case 'minus':
            return a-b;
        break;
    }
}
```
### 9. 类
##### 9.1 class
使用class这个关键词定义一个类,基于这个类创建实例以后会自动执行constructor方法,此方法可以用来初始化
```
class Person {
    constructor(name){
        this.name = name;//相当于原来的私有属性。
    }
    getName(){//相当于原来的原型上的属性。
        console.log(this.name);
    }
}
let person = new Person('xiaomi');
person.getName();//xiaomi
```

##### 9.2 getter setter
```
class Person {
    constructor(){
        this.hobbies = [];
    }
    set hobby(hobby){
        this.hobbies.push(hobby);
    }
    get hobby(){
        return this.hobbies;
    }
}
let person = new Person();
person.hobby = 'basketball';//set
person.hobby = 'football';//set
console.log(person.hobby);//["basketball", "football"],相当于执行get
```
##### 9.3 静态方法-static和继承extends
>+ 在类里面添加静态的方法可以使用static这个关键词，静态方法就是不需要实例化类就能使用的方法
```
class Person {
   static add(a,b){
       return a+b;
   }
}
console.log(Person.add(1,2));
//extend
class Person {
   constructor(name){
     this.name = name;
   }
}
class Teacher extends Person{
    constructor(name,age){
        super(name);
        this.age = age;
    }
}
var teacher = new Teacher('xiaomi,8)
console.log(teacher.name,teacher.age);//xiaomi 8
``` 

### Set 和Map
```
一个Set是一堆东西的集合,Set有点像数组,不过跟数组不一样的是，Set里面不能有重复的内容
var books = new Set();
books.add('js');
books.add('js');//添加重复元素集合的元素个数不会改变
books.add('html');
books.forEach(function(book){//循环集合
    console.log(book);//js//html
})
console.log(books.size);//集合中元数的个数,2
console.log(books.has('js'));//判断集合中是否有此元素true
books.delete('js');//从集合中删除此元素
console.log(books.size);//1
console.log(books.has('js'));//false
books.clear();//清空 set
console.log(books.size);//0
```
可以使用 Map 来组织这种名值对的数据
```
var books = new Map();
books.set('js',{name:'js'});//向map中添加元素
books.set('html',{name:'html'});
console.log(books.size);//查看集合中的元素
console.log(books.get('js'));//通过key获取值
books.delete('js');//执照key删除元素
console.log(books.has('js'));//判断map中有没有key
books.forEach((value, key) => { //forEach可以迭代map
    console.log( key + ' = ' + value);
});
books.clear();//清空map
```

###  11 模块