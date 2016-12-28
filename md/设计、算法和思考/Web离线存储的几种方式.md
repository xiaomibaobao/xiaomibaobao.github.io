# Web离线存储的几种方式

### 1、Application Cache
[Application Cache](http://www.w3schools.com/html/html5_app_cache.asp) 可以很简单让我们的WebApp具有离线的能力。

**支持的浏览器：**IE10+，FireFox，Chrome，Safari，Opera

**优点：**

1. 离线浏览 -- 用户可以再离线时使用Application
2. 速度 -- 由于缓存了资源，如果加载很快
3. 减少服务端数据加载 -- 浏览器只需要从服务器加载更新过的数据

**缺点：**

1. Manifest文件有变化时才更新
2. 一次必须更新Manifest中的所有文件，下次才生效

**如何使用？**

Step1:在html上指定manifest文件 (index.html)

	<html manifest="appCacheList.manifest">
	</html>

Step2:设定manifest文件内容 (appCache.manifest)

	CACHE MANIFEST

	# 离线缓存的内容
	./all.css
	./1.jpg
	./index.js
	
	# NETWORK:*，表示其他内容从网络获取
	NETWORK:
	*
	
	# 第一个uri是资源，第二个是fallback
	FALLBACK:
	/html/ /offline.html

手动更新缓存：

	if ( window.applicationCache.status == window.applicationCache.UPDATEREADY ){
	    window.applicationCache.update();
	}

**注意：**

1. 不同的浏览器对Application Cache的大小不一致，请注意。
2. 更多细节可参考[http://kayosite.com/web-app-by-jquery-mobile-and-html5-offline-web-applications.html](http://kayosite.com/web-app-by-jquery-mobile-and-html5-offline-web-applications.html)
	

### 2、Local Storage
[Local Storage](http://www.w3schools.com/html/html5_webstorage.asp)使得我们可以在浏览器中保存数据。

**支持的浏览器：**IE10+，FireFox，Chrome，Safari，Opera

**优点：**

1. 容量大
2. 易用
3. 强大
4. 原生支持
5. 仅存在本地，不会与服务器发生交互

**缺点：**

1. 浏览器兼容性差
2. 安全性差（不要存储敏感数据）


**如何使用？**

首先通过 ``window.localStorage`` 来判断浏览器是否支持Local Storage。然后由于该方式具有浏览器兼容性，建议用一个通用的库，来屏蔽兼容性。

	// 对基本方法的封装，需要判断浏览器，屏蔽它们的细节差异。
	(function(window){
	  if(!window.localStorage){
	    throw new Error('Your brower can\'t support local storage!');
	  }
	  var ls = window.localStorage;
	  var localStorageKit = {
	    getLength: function(){
	      return ls.length;
	    },
	    clear: function(){
	      ls.clear();
	      return true;
	    },
	    set: function(k, v){
	      ls.setItem(k, v);
	    },
	    get: function(k){
	      return ls.getItem(k);
	    },
	    remove: function(k){
	      ls.removeItem(k);
	    },
	    getKeyByIndex: function(index){
	      return ls.key(index);
	    }
	  };
	  window.lsKit = localStorageKit;
	})(window);

基本操作方式与cookie无太多差异。

**Session Storage：**
Session Storage和Local Storage非常类似，操作方式也一致。由于其中保存的存只是当前会话有效，那么此处就不细说。

### 3、Web SQL

[Web Sql Database](http://en.wikipedia.org/wiki/Web_SQL_Database)，是html5环境下可以用js执行CRUD的web数据库。数据库核心是SQLite。

**优点：**

1. 本地数据库
2. 可以处理复杂的关系型数据

**缺点：**

1. 暂时只有chrome才支持，对于Android大行其道的移动端，这应该是可以避免的缺点(貌似最新版本的Opera和Safari也支持了)

**如何使用？**

首先，先介绍Web sql的三个核心方法：

1. openDatabase：这个方法使用现有数据库或创建新数据库创建数据库对象。
2. transaction：这个方法允许我们根据情况控制事务提交或回滚。
3. executeSql：这个方法用于执行真实的SQL查询。

	var db = openDatabase('mydb', '1.0', 'Test DB', 2 * 1024 * 1024);
	var msg;
	db.transaction(function (tx) {
	  tx.executeSql('CREATE TABLE IF NOT EXISTS LOGS (id unique, log)');
	  tx.executeSql('INSERT INTO LOGS (id, log) VALUES (1, "foobar")');
	  tx.executeSql('INSERT INTO LOGS (id, log) VALUES (2, "logmsg")');
	  console.log('Log message created and row inserted.');
	});
	db.transaction(function (tx) {
	  tx.executeSql('SELECT * FROM LOGS', [], function (tx, results) {
	    var len = results.rows.length, i;
	    console.log('Found rows: ' + len);
	    for (i = 0; i < len; i++){
	      console.log(results.rows.item(i).log)
	    }
	  }, null);
	});

当成数据库用，就行。

### 4、IndexedDB
[IndexedDB](http://www.w3.org/TR/IndexedDB/)是结构化的本地数据存储。是基于平面文件的数据库，采用了分层的键值存储和基本的索引。

**优点：**

1. 标准化
2. 存储复杂数据
3. 支持索引

**缺点：**

1. 不支持SQL
2. 相对来说，操作较复杂

**如何使用？**

	// 打开数据库，第一个参数为数据库名，第二个为数据库版本号
	var dbRequest = window.indexedDB.open('testDb', 2);
	
	dbRequest.onupgradeneeded=function(e){
	    // 创建数据仓库
	    var db=e.target.result;
	    if(!db.objectStoreNames.contains('users')){
	        var store=db.createObjectStore('users',{keyPath: 'id'});
	        store.createIndex('nameIndex','name',{unique:true}); 
	        store.createIndex('ageIndex','age',{unique:false}); 
	    }
	    console.log('upgrade successfully!');
	};
	
	dbRequest.onsuccess = function(e){
	  console.log('Open database successfully!');
	  // 这里拿到了数据库
	  var db = e.target.result;
	  var storeName = 'users';
	  // 写入数据
	  var tran = db.transaction(storeName, 'readwrite');
	  var users = tran.objectStore(storeName);
	  for(var i = 0; i < 5; i++){
	    users.add({
	      id: i,
	      name: 'user' + i,
	      age: Math.floor(Math.random() * 10) + 18
	    });
	  }
	
	  //查询数据
	  var userStore = db.transaction(storeName).objectStore(storeName);
	  var request = userStore.openCursor();
	  request.onsuccess = function(e){
	    var cursor = e.target.result;
	    if(cursor){
	      console.log(cursor.key);
	      console.log(cursor.value);
	      cursor.continue();
	    }
	  }
	}

### 其他

[HTML 5中几种用于在客户端本地存储数据的API之间的比较](http://html5online.com.cn/articles/2012080901.html)

[HTML5本地存储——IndexedDB（一：基本使用）](http://www.cnblogs.com/dolphinX/p/3415761.html)

[HTML5本地存储——IndexedDB（二：索引）](http://www.cnblogs.com/dolphinX/p/3416889.html)