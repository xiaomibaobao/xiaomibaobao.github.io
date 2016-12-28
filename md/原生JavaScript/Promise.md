##Promise
### 1. 异步回调
### 1.1 回调地狱
在需要多个操作的时候，会导致多个回调函数嵌套，导致代码不够直观，就是常说的回调地狱
```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>move</title>
    <style>
        .square{
            width:40px;
            height:40px;
        }
        .square1{
            background-color: red;
        }
        .square2{
            background-color: yellow;
        }
        .square3{
            background-color: blue;
        }
    </style>
</head>
<body>
<div class="square square1" style="margin-left: 0"></div>
<div class="square square2" style="margin-left: 0"></div>
<div class="square square3" style="margin-left: 0"></div>
</body>
<script>
    var square1 = document.querySelector('.square1');
    var square2 = document.querySelector('.square2');
    var square3 = document.querySelector('.square3');
    function move(element,target,cb){
        setTimeout(function () {
            var marginLeft = parseInt(element.style.marginLeft, 10);
            if(marginLeft == target){
                cb();
            }else{
                element.style.marginLeft = ++marginLeft+'px';
                move(element,target,cb);
            }
        },13);
    }
    move(square1,100,function(){
        move(square2,100,function(){
            move(square3,100);
        });
    });
</script>
</html>
```
### 1.2 并行结果
如果几个异步操作之间并没有前后顺序之分,但需要等多个异步操作都完成后才能执行后续的任务，无法实现并行节约时间

## 2. Promise
Promise本意是承诺，在程序中的意思就是承诺我**过一段时间后**会给你一个结果。
什么时候会用到**过一段时间**？答案是异步操作，异步是指可能比较长时间才有结果的才做，例如网络请求、读取本地文件等

## 3. Promise的三种状态
- Pending Promise对象实例创建时候的初始状态
- Fulfilled 可以理解为成功的状态
- Rejected 可以理解为失败的状态

> then 方法就是用来指定Promise 对象的状态改变时确定执行的操作，resolve 时执行第一个函数（onFulfilled），reject 时执行第二个函数（onRejected）

## 4. 构造一个Promise
```
let promise = new Promise((resolve, reject) => {
	setTimeout(() => {
	    if(Math.random()>0.5)
		    resolve('This is resolve!');
		else 
		    reject('This is reject!');
	}, 1000);
});
promise.then(Fulfilled,Rejected)
```
- 构造一个Promise实例需要给Promise构造函数传入一个函数。
- 传入的函数需要有两个形参，两个形参都是function类型的参数。
    - 第一个形参运行后会让Promise实例处于resolve状态，所以我们一般给第一个形参命名为resolve,使 Promise 对象的状态改变成成功，同时传递一个参数用于后续成功后的操作
    - 第一个形参运行后会让Promise实例处于reject状态，所以我们一般给第一个形参命名为reject,将 Promise 对象的状态改变为失败，同时将错误的信息传递到后续错误处理的操作

## 5. promise 做为函数的返回值
```
function ajaxPromise (queryUrl) {
  return new Promise((resolve, reject) => {
    let xhr = new XMLHttpRequest();
    xhr.open('GET', queryUrl, true);
    xhr.send(null);
    xhr.onreadystatechange = () => {
      if (xhr.readyState === 4) {
        if (xhr.status === 200) {
          resolve(xhr.responseText);
        } else {
          reject(xhr.responseText);
        }
      }
    }
  });
}

ajaxPromise('http://www.baidu.com')
  .then((value) => {
    console.log(value);
  })
  .catch((err) => {
    console.error(err);
  });
```



## 6.promise的链式调用
- 每次调用返回的都是一个新的Promise实例
- 链式调用的参数通过返回值传递

`then`可以使用链式调用的写法原因在于，每一次执行该方法时总是会返回一个`Promise`对象
```
readFile('1.txt').then(function (data) {
    console.log(data);
    return data;
}).then(function (data) {
    console.log(data);
    return readFile(data);
}).then(function (data) {
    console.log(data);
}).catch(function(err){
 console.log(err);
});
```
## 7.promise API
### 7.1 Promise.all
- **参数**：接受一个数组，数组内都是`Promise`实例
- **返回值**：返回一个`Promise`实例，这个`Promise`实例的状态转移取决于参数的`Promise`实例的状态变化。当参数中所有的实例都处于`resolve`状态时，返回的`Promise`实例会变为`resolve`状态。如果参数中任意一个实例处于`reject`状态，返回的`Promise`实例变为`reject`状态。
```
Promise.all([p1, p2]).then(function (result) {
    console.log(result); // [ '2.txt', '2' ]
});
```
>  不管两个promise谁先完成，Promise.all 方法会按照数组里面的顺序将结果返回

### 7.2 Promise.race
- **参数**：接受一个数组，数组内都是`Promise`实例
- **返回值**：返回一个`Promise`实例，这个`Promise`实例的状态转移取决于参数的`Promise`实例的状态变化。当参数中任何一个实例处于`resolve`状态时，返回的`Promise`实例会变为`resolve`状态。如果参数中任意一个实例处于`reject`状态，返回的`Promise`实例变为`reject`状态。
```
Promise.race([p1, p2]).then(function (result) {
    console.log(result); // [ '2.txt', '2' ]
});
```
### 7.3 Promise.resolve
返回一个`Promise`实例，这个实例处于`resolve`状态。

根据传入的参数不同有不同的功能：

- 值(对象、数组、字符串等)：作为`resolve`传递出去的值
- `Promise`实例：原封不动返回

### 7.4 Promise.reject
返回一个`Promise`实例，这个实例处于`reject`状态。

- 参数一般就是抛出的错误信息。


## 8. q的用法
```
var Q = require('q');
var deferred = Q.defer();
  setTimeout(function() {
     deferred.resolve();
  }, 5000);
  return deferred.promise;
```

## 9. bluebird
实现 promise 标准的库是功能最全，速度最快的一个库
```
var Promise = require('bluebird');
var fs = require('fs');
fs.readFileAsync = Promise.promisify(fs.readFile, fs);
fs.readFileAsync('LICENSE','utf8').then(function(data){
    console.log(data);
});
```

执行完下面的代码之后，fs对象下所有的异步方法都会对应的生成一个Promise版本方法，比如fs.readFile对应fs.readFileAsync，fs.mkdir对应fs.mkdirAsync
```
Promise.promisifyAll(fs);
```

## 10. promise/a+实现原理
```
var defer = function () {
    var pending = [], value;
    return {
        resolve: function (_value) {
            if (pending) {
                value = _value;
                for (var i = 0, ii = pending.length; i < ii; i++) {
                    var callback = pending[i];
                    callback(value);
                }
                pending = undefined;
            }
        },
        promise: {
            then: function (callback) {
                if (pending) {
                    pending.push(callback);
                } else {
                    callback(value);
                }
            }
        }
    };
};
```


## 11. 动画之promise版
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>move</title>
    <style>
        .square{
            width:40px;
            height:40px;
        }
        .square1{
            background-color: red;
        }
        .square2{
            background-color: yellow;
        }
        .square3{
            background-color: blue;
        }
    </style>
</head>
<body>
<div class="square square1" style="margin-left: 0"></div>
<div class="square square2" style="margin-left: 0"></div>
<div class="square square3" style="margin-left: 0"></div>
</body>
<script>
    var square1 = document.querySelector('.square1');
    var square2 = document.querySelector('.square2');
    var square3 = document.querySelector('.square3');
    function move(element,target,cb){
        setTimeout(function () {
            var marginLeft = parseInt(element.style.marginLeft, 10);
            if(marginLeft == target){
                cb();
            }else{
                element.style.marginLeft = ++marginLeft+'px';
                move(element,target,cb);
            }
        },13);
    }
    function animate(element,target){
        return new Promise(function(resolve,reject){
            move(element,target,resolve);
        });
    }
    animate(square1,100)
    .then(function(){
        return animate(square2,100);
    })
    .then(function(){
        return animate(square3,100);
    });
</script>
</html>
```



