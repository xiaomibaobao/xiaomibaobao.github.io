## MongoDB
### 1.1 MongoDB简介
MongoDB是一个开源的NoSQL数据库
同时它也是一个对象数据库，没有固定的模式和结构  
所有的数据以文档的形式存储,数据格式就是JSON。

### 1.2 Mongoose是什么？

Mongoose是MongoDB的一个对象模型工具  
同时它也是针对MongoDB操作的一个对象模型库,封装了MongoDB对文档的的一些增删改查等常用方法   
让NodeJS操作Mongodb数据库变得更加灵活简单  

### 1.3 安装mongoose
```javascript
$ npm install mongoose
```

### 1.4 使用mongoose
```javascript
var mongoose = require("mongoose");
mongoose.connect("mongodb://user:pass@localhost:port/database");
```
### 1.5 Schema

Schema是数据库集合的模型骨架  
定义了集合中的字段的名称和类型以及默认值等信息   

#### 1.5.1 Schema.Type
NodeJS中的基本数据类型都属于Schema.Type  
另外Mongoose还定义了自己的类型    
基本属性类型有:  
- 字符串(String)
- 日期型(Date)
- 数值型(Number)
- 布尔型(Boolean)
- null
- 数组([])
- 内嵌文档

#### 1.5.2 定义Schema
```javascript
    var personSchema = new Schema({
      name:String, //姓名
      binary:Buffer,//二进制
      living:Boolean,//是否活着
      birthday:Date,//生日
      age:Number,//年龄
      _id:Schema.Types.ObjectId,  //主键
      _fk:Schema.Types.ObjectId,  //外键
      array:[],//数组
      arrOfString:[String],//字符串数组
      arrOfNumber:[Number],//数字数组
      arrOfDate:[Date],//日期数组
      arrOfBuffer:[Buffer],//Buffer数组
      arrOfBoolean:[Boolean],//布尔值数组
      arrOfObjectId:[Schema.Types.ObjectId]//对象ID数组
      nested:{ //内嵌文档
        name:String,
      }
    });
```

### 1.6 Model
Model是由通过Schema构造而成  
除了具有Schema定义的数据库骨架以外，还可以操作数据库   
如何通过Schema来创建Model呢，如下:    
```javascript
mongoose.connect("mongodb://123.57.143.189:27017/xiaomi");
var PersonModel = db.model("person", PersonSchema);//两个参数表示定义一个模型
// 如果该Model已经定义，则可以直接通过名字获取
var pModel = db.model('person');//一个参数表示获取已定义的模型

```
拥有了Model，我们也就拥有了操作数据库的能力
> 

### 1.7 Entity简述
通过Model创建的实体，它也可以操作数据库  
使用Model创建Entity，如下示例：
```javascript
 var personEntity = new PersonModel({
     name : "xiaomi",
     age  : 6,
     email: "xiaomi@qq.com"
 });
```

> Schema生成Model，Model创造Entity，Model和Entity都可对数据库操作,但Model比Entity更具操作性

### 1.8 保存Entity
```javascript
var mongoose = require("mongoose");
mongoose.connect("mongodb://123.57.143.189:27017/xiaomi");
var PersonSchema = new mongoose.Schema({
    name: {type: String},
    age: {type: Number, default: 0},
    time: {type: Date, default: Date.now()},
    email: {type: String, default: ''}
});
var PersonModel = db.model("person", PersonSchema);

var personEntity = new PersonModel({
    name: "xiaomi",
    age: 6,
    email: "xiaomi@qq.com"
});
console.log(personEntity.name);
personEntity.save(function (error, doc) {
    if (error) {
        console.log("error :" + error);
    } else {
        console.log(doc);
    }
});
```

### 1.9 ObjectId
存储在mongodb集合中的每个文档都有一个默认的主键_id  
这个主键名称是固定的，它可以是mongodb支持的任何数据类型，默认是ObjectId  该类型的值由系统自己生成，从某种意义上几乎不会重复  
ObjectId是一个12字节的 BSON 类型字符串。按照字节顺序，依次代表：  
- 4字节：UNIX时间戳
- 3字节：表示运行MongoDB的机器
- 2字节：表示生成此_id的进程
- 3字节：由一个随机数开始的计数器生成的值

> 每一个文档都有一个特殊的键“_id”，这个键在文档所属的集合中是唯一的。


### 1.10 为Entity增加实例方法
```javascript
PersonSchema.methods.findSameAge = function(cb){
      return this.model('Person').find({age:this.age},cb);
}
var PersonModel = db.model("person", PersonSchema);
 var personEntity = new PersonModel({
     name : "xiaomi",
     age  : 6,
     email: "xiaomi@qq.com"
 });
 personEntity.findSameAge(function(err,docs){
    console.log(docs);
 });
```

### 1.11 为Model增加静态方法
```javascript
PersonSchema.statics.findByName = function(name,cb){
    this.find({name:new RegExp(name,'i')},cb);
}
var PersonModel = mongoose.model('Person',PersonSchema);
PersonModel.findByName('xiaomi',function(err,persons){
     console.log(persons);//获得所有名称叫珠峰培训的人
});
```

### 1.12 验证器
验证器是指定在数据保存到数据库之前做的验证
- required 非空验证
- min/max 范围验证（边值验证）
- enum/match 枚举验证/匹配验证

```javascript
var PersonSchema = new Schema({
      name:{
        type:'String',
        required:true //姓名非空
      },
      age:{
        type:'Nunmer',
        min:18,       //年龄最小18
        max:110     //年龄最大110
      },
      city:{
        type:'String',
        enum:['北京','广州']  //只能是北京或广州
      }
    });
```

### 1.13 中间件
中间件是一种控制函数，类似插件，能控制流程中的init、validate、save、remove`方法
#### 1.13.1 Serial串行
串行使用pre方法，执行下一个方法使用next调用
```javascript
var schema = new Schema(...);
schema.pre('save',function(next){
      //做点什么
      next();
});
```
#### 1.13.1 Parallel并行
```javascript
schema.pre('save',function(next,done){
      //下一个要执行的中间件并行执行
      next();
      doAsync(done);
});
```


## 2. 基础操作
### 2.1 查询
语法
```javascript
Model.find(查询条件,callback);
```
代码
```javascript
Model.find({},function(error,docs){
  //若没有向find传递参数，默认的是显示所有文档
});
     
Model.find({ "age": 6 }, function (error, docs) {
  if(error){
    console.log("error :" + error);
  }else{
    console.log(docs); //docs: age为6的所有文档
  }
}); 
```
### 2.2 Model保存
语法
```javascript
Model.create(文档数据, callback))
```
代码
```javascript
 PersonModel.create({ name:"xiaomi", age:7}, function(error,doc){
    if(error) {
        console.log(error);
    } else {
        console.log(doc);
    }
});
```

### 2.3 Entity保存
语法
```javascript
Entity.save(文档数据, callback))
```
代码
```javascript
var PersonEntity = new PersonModel({name:"xiaomi",age: 9});
   
PersonEntity.save(function(error,doc) {
   if(error) {
      console.log(error);
   } else {
      console.log(doc);
   }
});
```

### 2.4 更新
语法
```javascript
Model.update(查询条件,更新对象,callback);
```
代码
```javascript
var conditions = {name : 'xiaomi'};
  var update = {$set : { age : 100 }};
  PersonModel.update(conditions, update, function(error){
      if(error) {
          console.log(error);
      } else {
          console.log('Update success!');
        }
    });
```
> 请注意如果匹配到多条记录，默认只更新一条，如果要更新匹配到的所有记录的话需要加一个参数 {multi:true}

### 2.5 删除
语法
```
Model.remove(查询条件,callback);
```
代码
```javascript
var conditions = { name: 'xiaomi' };
PersonModel.remove(conditions, function(error){
    if(error) {
          console.log(error);
    } else {
        console.log('Delete success!');
    }
});
```

## 3. 基本查询
### 3.1 准备数据
```javascript
        PersonModel.create([
                          { name:"xiaomi1", age:1 },
                          { name:"xiaomi2", age:2 },
                          { name:"xiaomi3", age:3 },
                          { name:"xiaomi4", age:4 },
                          { name:"xiaomi5", age:5 },
                          { name:"xiaomi6", age:6},
                          { name:"xiaomi7", age:7 },
                          { name:"xiaomi8", age:8 },
                          { name:"xiaomi9", age:9},
                          { name:"xiaomi10",age:10 }
                         ], function(error,docs) {
            if(error) {
                console.log(error);
            } else {
                console.log('save ok');
            }
        });
```
### 3.2 属性过滤
语法
```javascript
find(Conditions,field,callback)
```
代码
```javascript
//field省略或为Null，则返回所有属性。
//返回只包含name、age两个键的所有记录
Model.find({},{name:1, age:1, _id:0}，function(err,docs){
   //docs 查询结果集
})
```
> 我们只需要把显示的属性设置为大于零的数就可以，当然1是最好理解的，_id是默认返回，如果不要显示加上("_id":0)

### 3.3 findOne(查询单条)
与find相同，但只返回单个文档，也就说当查询到即一个符合条件的数据时，将停止继续查询，并返回查询结果
语法
```javascript
findOne(Conditions,callback)
```
代码
```javascript
TestModel.findOne({ age: 6}, function (err, doc){
       // 查询符合age等于6的第一条数据
       // doc是查询结果
});
```

### 3.4 findById(按ID单条数据)
与findOne相同，但它只接收文档的_id作为参数，返回单个文档
语法
```javascript
findById(_id, callback)
```
代码
```javascript
PersonModel.findById(person._id, function (err, doc){
     //doc 查询结果文档
});
```

### 3.5 $gt、$lt(大于、小于) 
查询时我们经常会碰到要根据某些字段进行条件筛选查询，比如说Number类型，怎么办呢，我们就可以使用$gt(>)、$lt(<)、$lte(<=)、$gte(>=)操作符进行排除性的查询，如下示例：
```javascript
Model.find({"age":{"$gt":6}},function(error,docs){
   //查询所有nage大于6的数据
});
     
Model.find({"age":{"$lt":6}},function(error,docs){
   //查询所有nage小于6的数据
});
     
Model.find({"age":{"$gt":6,"$lt":9}},function(error,docs){
  //查询所有nage大于6小于9的数据
});
```

### 3.6 $ne(不等于)
$ne(!=)操作符的含义相当于不等于、不包含，查询时我们可通过它进行条件判定，具体使用方法如下：
```javascript
Model.find({ age:{ $ne:6}},function(error,docs){
  //查询age不等于6的所有数据
});
```
### 3.7 $in(包含)
和$ne操作符相反，$in相当于包含、等于，查询时查找包含于指定字段条件的数据
```javascript
Model.find({ age:{ $in: 6}},function(error,docs){
   //查询age等于6的所有数据
});
 
Model.find({ age:{$in:[6,7]}},function(error,docs){
  //可以把多个值组织成一个数组
}); 
```
### 3.8  $or(或者)
可以查询多个键值的任意给定值，只要满足其中一个就可返回，用于存在多个条件判定的情况下使用，如下示例：
```javascript
Model.find({"$or":[{"name":"xiaomi"},{"age":6}]},function(error,docs){
    //查询name为xiaomi或age为6的全部文档
});
```

### 3.9 $exists(是否存在)
$exists操作符，可用于判断某些关键字段是否存在来进行条件查询。如下示例：
```javascript
Model.find({name: {$exists: true}},function(error,docs){
      //查询所有存在name属性的文档
});
     
Model.find({email: {$exists: false}},function(error,docs){
      //查询所有不存在email属性的文档
});
```
## 4. 高级查询
可以限制结果的数量,跳过部分结果,根据任意键对结果进行各种排序  
> 所有这些选项都要在查询被发送到服务器之前指定 

### 4.1 limit(限制数量)
在查询操作中,有时数据量会很大,这时我们就需要对返回结果的数量进行限制  
那么我们就可以使用limit函数，通过它来限制结果数量。
语法
```javascript
find(Conditions,fields,options,callback);
```
代码
```javascript
Model.find({},null,{limit:20},function(err,docs){
        console.log(docs);
});
```
> 如果匹配的结果不到20个，则返回匹配数量的结果，也就是说limit函数指定的是上限而非下限

### 4.2 skip(跳过/略过的数量)
skip函数的功能是略过指定数量的匹配结果，返回余下的查询结果  
如下示例：
```javascript
find(Conditions,fields,options,callback);
```
代码
```javascript
Model.find({},null,{skip:4},function(err,docs){
        console.log(docs);
});
```
> 如果查询结果数量中少于4个的话，则不会返回任何结果。

### 4.3 sort函数
sort函数可以将查询结果数据进行排序操作  
该函数的参数是一个或多个键/值对  
键代表要排序的键名,值代表排序的方向,1是升序,-1是降序  
语法
```javascript
find(Conditions,fields,options,callback)
```
代码
```javascript
 Model.find({},null,{sort:{age:-1}},function(err,docs){
      //查询所有数据，并按照age降序顺序返回数据docs
});
```
> sort函数可根据用户自定义条件有选择性的来进行排序显示数据结果。

### 4.4 分页查询
```javascript
Model('User').find({})
  .sort({createAt:-1})
  .skip((pageNum-1)*pageSize)
  .limit(pageSize)
  .populate('user')
  .exec(function(err,docs){
     console.log(docs);
  });

```

## 5. 扩展阅读
[mongoose](http://www.nodeclass.com/api/mongoose.html)



