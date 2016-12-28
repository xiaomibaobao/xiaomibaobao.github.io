# JS 基础知识拾遗

### JS中的数据类型：
1. 基本数据类型：
	 -  数字：
```
	如何判断一个值是否是有效数字，isNaN，如果是有效数字，则返回false，它分为两步，先强制转换为数类型，再判断。
	isFinite()判断一个数是否是一个既非infinity也非NaN的数字。
    isFinite（12）--》true；isFinite（le308）true，isFinite（le309）；false，Javascript中最大数字是1.7976e+308，le309会被视作无穷（infinity）
```
- 布尔：
```
 ! 如果前面有！，一定会先进行布尔转换。优先级最高。
  !!:强制转换boolean。
  if（运算表达式)
  if（单独的值）
  while（表达式或者值）//以上三者都是判断括号中的情况再进行下一步操作。
```
- 字符串：
```

```
- null：
```
null是一种标示型数据，表示现在没有，未来会有的，暂时表示没有值（对象）、可以理解为空指针。
（1） 作为函数的参数，表示该函数的参数不是对象。     
（2） 作为对象原型链的终点。
（3）RegExp.exec没找到。console.log(/\d+/g.exec('aaabbb')) //null'
typeof null 结果是object。
```
- undefined：
```
undefined是标示型数据类型，压根没有过。
（1）变量被声明了，但没有赋值时，就等于undefined。
（2) 调用函数时，应该提供的参数没有提供，该参数等于undefined。
（3）对象没有赋值的属性，该属性的值为undefined。
（4）函数没有返回值时，没有写return默认返回undefined。
typeof undefined 结果为undefined
```
2. 引用数据类型：
object类：
object：
array：
正则：
math：
date：

function类：

### 各个类型详解
1. 数字：
	**NaN**
    它是一个笼统的概念，不是表示一个具体的值，和任何值比较，都不想等，包括其自己。他不是一个具体的值，即NaN！=NaN,为true，
     它出现在：1，做数学运算失败的时候；2，强制将其他数据类型转换成number类型失败的时候
	**转换方法：**
`Number()`；强行转换；
`parseInt（）` 和`parseFloat（）`是专门将字符串转换成数类型。前者只提取整数。后者除了整数，还有小数。
布尔转数字：true=1，false=0
Number()-->0  ; Number([]) -->0;  Number({})--> NaN  ;
Number（null）--> 0  ; Number(undefined)  --> NaN ;
其他的字符串，除“.”以外，只有一个字符不是数字，结果就是NaN。
2.  字符串String：
1，**charAt ** 通过索引来查找对应的字符或者编码
var str="Hello world!"
document.write(str.charAt(1))
在字符串 "Hello world!" 中，我们将返回位置 1 的字符：
index 只接受一个。默认是0位置，负数则返回空字符串，
**charCodeAt**：指定位置上字符的Unicode编码，如果 index 是负数，或大于等于字符串的长度，则 charCodeAt() 返回 NaN。
2， **indexOf ** 通过字符串来查找下表/索引。
**lastIndexOf**：查找指定的子字符串在字符串中最后一次出现的位置，和indexof一样，若没找到，返回-1
== 的反义词是 ！=，===反义词！==，在JS中没有！===。
3，字符串截取
**substring**（0,2），从第N个索引开始截取到第m个，包前不包后。
**slice**，和substring相同，但可以负值截取，此时负值为：-1指代最后一位。-2为倒数第二位。若负值<=字符串长度，则全部截取。只有一个参数的话，从参数截取到末尾。非法参数会转换成0.）
**substr ** str.substr(start,length)可在字符串中抽取从 start 下标开始的指定数目的字符。
4，字符串拆分--》字符串转数组。
**split**（“切分依据”）。返回分割完成后的数组。
stringObject.split(separator,howmany)
把字符串切成数组，一个值，则以separator为依据，分割两边。如不指定，则直接转化成数组，
5，字符串替换
str. **replace** ("oldStr","newStr")
6,大小写转换
**toUpperCase toLowerCase**
7，**trim**（）去除字符串首位的所有空格，但中间的会保留。html5新方法，
8，**< >**直接比较unicode

3. 布尔Boolean
Boolean（）可以强制将其他数据类型转换成boolean类型。！！也可以强制转换。
0 NaN null undefined 空字符串（[ ]）转换为boolean的时候，结果是false，其他的都是true。
（！a）感叹号取反，会现将数据转换为boolean类型，然后取反。

4. 数组Array
- 原数组发生了改变。
1，
push（）往数组末尾中添加内容。参数：即添加的内容。返回值：添加完成之后数组的长度。
unshift（）将内容添加到数组的首位，参数是添加的内容，返回值是添加内容后，数组的长度。
pop（）把数组的最后一项删除。无参数。返回值是删除的内容。
shift（）删除数组的第一项内容，无参数，。。。。是删除的内容。
2，   
原生的往数组末尾添加一项内容和删除最后一项内容的方法：
  var a
  增加： ary[ary.length]=5//假如ary.length=5，最后一项的索引就是length。
  删除：ary.length--.,
3，
splice(n),从索引n的位置，包括n，一直删除到最后。返回值是删除的内容，
splice（n,m）从索引N的位置，从n开始删除m个内容。返回值都是。。。。。不包括n，相当于从0开始删除一项。
splice（n,m,x）从索引n的位置，从n开始删除m个内容，用x（可以是一项，可以是多项）的内容替换删除的内容，
                          逻辑是：添加到删除的内容的前面。特别是第二个是参数是0的时候。。
                          返回值是删除后的内容，
4，
reverse（），将数组倒着排列。返回值是倒着排列后的新数组。
      sort（）
```
arr.sort()//默认从小到大排列。只适用10以内数字的数组；他们只按数组中数字首个数字排序。
    arr.sort(-1)//若传入的值（可以是函数）小于零，则顺序排列，即不动。
    arr.sort(1)//反之，如果大于0，则相当于reverse
    arr.sort(function(a,b){return a-b})//从小到大，若return b-a是从大到小。可用于大于10的数组排序。

```

二：原数组不发生改变。
1，slice（n）从n开始截取，一直截取到最后，“. ”.不算。
     slice（n，m）从n开始截取，一直到索引m，（有n没m），
     返回值是截取后新的数组，原数组不发生改变。
       slice的参数可以是负数，（这时索引=负数索引+数组的长度） 
      indexof（n）从数组的前面往后找，如果不能找到返回-1，如果能找到，返回资格字符串的索引值。只找一个，找到就停止。参数n表示数组某一项内容，并非索引。
      lastindexof（）从后往前。。。。。。。。。。。。。。。。。。
     这两个ie6-8不兼容。可以判断数组里是否有这一项存在。
for in；
 join（）按照指定的字符将数组拼接成字符串。参数即字符串拼接方法，
   toString（）把数组转化为字符串。
               、//不是数组则用：eval（），把字符串转换成js语句，相当于在控制台内写字符串。会直接计算，比如求和concat（）将多个数组拼接成一个数组，ary1.concat（ary2，ary3）
var ary = [12, 23, 34];
    forEach:用来遍历数组中的每一项的,数组中有几项我们就遍历几次,每一次遍历都会把传递进来的匿名回调函数执行一次,但是每一次执行都给这个回调函数传递了三个参数值 
      **ary.forEach**(function (item, index, input) {
           //console.log(item, index, input);//->当前遍历的这一项、当前这一项的索引、原来的数组
           console.log(this);//this->window
       });
    ary.forEach(function (item, index, input) {
        console.log(this);//this->obj
    }, obj);//->forEach中传入的第二个参数的意思:把匿名回调函数中的THIS改变为obj,不传第二个参数默认的this是window。

5. 数据类型比较
两边数据类型不一样的时候，==比较就会发生数据类型变化。
1，**对象和对象比较**的时候，比较的是引用地址。
[]==[]//false;
2,**对象和字符串 **的比较，会先将对象转换成字符串再比较，默认调用toString方法，然后再进行比较。
[1]=="1"//true;
3，**对象==布尔** 时；左右两边都转换成相同的数据类型（数类型），然后再比较，这时：对象首先转换成字符串，然后再转换成数类型，布尔是通过number（）强制转换成数类型。
[]==false// true;
4，**对象==数字**，直接把对象转换成数类型。
[]==1//false
     字符串==数字 字符串转成数类型。
5，**字符串==布尔**，左右两边都转换成相同的数据类型（数类型），然后再比较，这时：字符串转换成数类型，布尔是通过number（）强制转换成数类型。
6，NaN！=NaN；
      undefined==null// true；
       undefined===null//false;
null或者undefined和其他数去比较的时候，其他全是false。

6. Math：天生全局对象
Math.abs（）求绝对值。
Math.random()0-1之间的随机数。包含0，不包含1.
          //取0-60的随机数，Math.random()*60
         //求n-m之间的随机数，Math.random()*(m-n)+n;
        //取2-62之间随机的四个不重复的整数，包括2和62    笔记。
Math.round()四舍五入。
Math.sqrt()开平方
Math,pow(a,b)a 的B次方
Math.tan()
Math.atan2();
Math.min(1,3,4)一组数据中取到最小值。
            max
Math.floor();向下取整。  
Math.ceil()向上取整。  ceil天花板
toFixed（n）保留N位数，小数点之后。带四舍五入。

7. 运算顺序
但逻辑中的！会高于一切。
算数》比较》逻辑》赋值
或运算||，&&且运算。
a||b ,若a为真，则返回a的值，若a为假，则返回b的值（不管B的真假）
a&&b，若a 为真，则 返回b的值，若a为假，则返回a的值（也不管b的值）

并且和或放在一起的话，并且大于或。
0&&3||5-->5
3||0&&5  3

++i,先拿i的值进行运算，运算完成本身+1，
i++，先本身累加1，然后拿累加完成的结果去运算。

var i=5;
console.log(1 + (i++));//6,但是i=6，先运算了1+i，然后再给i++并赋值。
console.log(1+(++i));//7，先i++给i赋值6，然后再i+1.
console.log(2+(++i)+(i++)=(i++)=(++i))

### 循环和判断：
#### 循环：
for循环：while循环：
for(i=x;条件；i++){
  执行语句
}
for和while的相同点和不同点：
相同点：定义，条件，语句，自增或者自检。
不同点：for用在循环次数确定，while用在不确定。
do while循环，while循环至少执行一次。
do{执行语句}while（条件）
for in 循环--主要用于对象。

#### 判断：
1，if （条件一）{情况一} else if（条件二）{情况二} else{其余情况}
2  switch（需要判断的情况） case（情况）  break default       case比较是三个等号的比较（严格比较）。
3，三元/三木表达式。条件？成立的语句一：失败语句二。
