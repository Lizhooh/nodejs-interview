## nodejs面试题整理


* **ES6有哪些新特性**

	1. 类的支持
	2. 模块化
	3. 箭头函数
	4. let/const块作用域
	5. 模板字符串
	6. 解构
	7. 参数默认值/不定参数/扩展参数
	8. for-of遍历
	9. generator
	10. Map/Set
	11. Promise
	12. 延展操作符
	
	> ``async/await`` 在es8中加入

---

* **常用js类定义的方法有哪些？**

	> 主要有构造函数原型和对象创建两种方法。原型法是通用老方法，对象创建是ES5推荐使用的方法.目前来看，原型法更普遍.

	1、构造函数方法定义类
	
	```
	function Person(){
		this.name = 'michaelqin';
	}
	Person.prototype.sayName = function(){
		alert(this.name);
	}
	
	var person = new Person();
	person.sayName();
	```
	
	2、对象创建方法定义类
	
	```
	var Person = {
		name: 'michaelqin',
		sayName: function(){ alert(this.name); }
	};
	
	var person = Object.create(Person);
	person.sayName();
	```

---

*  **js类继承的方法有哪些**

	> 原型链法，属性复制法和构造器应用法. 另外，由于每个对象可以是一个类，这些方法也可以用于对象类的继承．

	1、原型链法
	
	```
	function Animal() {
		this.name = 'animal';
	}
	Animal.prototype.sayName = function(){
		alert(this.name);
	};
	
	function Person() {}
	Person.prototype = Animal.prototype; // 人继承自动物
	Person.prototype.constructor = 'Person'; // 更新构造函数为人
	```

	2、属性复制法

	```
	function Animal() {
		this.name = 'animal';
	}
	Animal.prototype.sayName = function() {
		alert(this.name);
	};
	
	function Person() {}
	
	for(prop in Animal.prototype) {
		Person.prototype[prop] = Animal.prototype[prop];
	} // 复制动物的所有属性到人量边
	Person.prototype.constructor = 'Person'; // 更新构造函数为人
	```

	3、构造器应用法
	
	```
	function Animal() {
		this.name = 'animal';
	}
	Animal.prototype.sayName = function() {
		alert(this.name);
	};
	
	function Person() {
		Animal.call(this); // apply, call, bind方法都可以．细微区别，后面会提到．
	}
	```

---

* **js类多重继承的实现方法是怎么样的?**

	就是类继承里边的属性复制法来实现．因为当所有父类的prototype属性被复制后，子类自然拥有类似行为和属性．

---

* **``apply``, ``call``和``bind``有什么区别?**

	> 三者都可以把一个函数应用到其他对象上，注意不是自身对象．apply,call是直接执行函数调用，bind是绑定，执行需要再次调用．apply和call的区别是apply接受数组作为参数，而call是接受逗号分隔的无限多个参数列表，

	```
	function Person() {
	}
	Person.prototype.sayName() { alert(this.name); }
	
	var obj = {name: 'michaelqin'}; // 注意这是一个普通对象，它不是Person的实例
	1) apply
	Person.prototype.sayName.apply(obj, [param1, param2, param3]);
	
	2) call
	Person.prototype.sayName.call(obj, param1, param2, param3);
	
	3) bind
	var sn = Person.prototype.sayName.bind(obj);
	sn([param1, param2, param3]); // bind需要先绑定，再执行
	sn(param1, param2, param3); // bind需要先绑定，再执行
	```

---

* **``defineProperty``, ``hasOwnProperty``, ``propertyIsEnumerable``都是做什么用的？**

	``Object.defineProperty(obj, prop, descriptor)``用来给对象定义属性,有``value``,``writable``,``configurable``,``enumerable``,``set/get``等.``hasOwnProerty``用于检查某一属性是不是存在于对象本身，继承来的父亲的属性不算．``propertyIsEnumerable``用来检测某一属性是否可遍历，也就是能不能用``for..in``循环来取到.

---

* **js常用设计模式的实现思路，单例，工厂，代理，装饰，观察者模式等**

	1) 单例：　任意对象都是单例，无须特别处理

	```
	var obj = {name: 'michaelqin', age: 30};
	```

	2) 工厂: 就是同样形式参数返回不同的实例

	```
	function Person() { this.name = 'Person1'; }
	function Animal() { this.name = 'Animal1'; }
	
	function Factory() {}
	Factory.prototype.getInstance = function(className) {
		return eval('new ' + className + '()');
	}
	
	var factory = new Factory();
	var obj1 = factory.getInstance('Person');
	var obj2 = factory.getInstance('Animal');
	console.log(obj1.name); // Person1
	console.log(obj2.name); // Animal1
	```

	3) 代理: 就是新建个类调用老类的接口,包一下
	
	```
	function Person() { }
	Person.prototype.sayName = function() { 
		console.log('michaelqin'); 
	}
	Person.prototype.sayAge = function() { console.log(30); }
	
	function PersonProxy() {
		this.person = new Person();
		var that = this;
		this.callMethod = function(functionName) {
			console.log('before proxy:', functionName);
			that.person[functionName](); // 代理
			console.log('after proxy:', functionName);
		}
	}
	
	var pp = new PersonProxy();
	pp.callMethod('sayName'); // 代理调用Person的方法sayName()
	pp.callMethod('sayAge'); // 代理调用Person的方法sayAge()
	```

	4) 观察者: 就是事件模式，比如按钮的onclick这样的应用.
	
	```
	function Publisher() {
		this.listeners = [];
	}
	Publisher.prototype = {
		'addListener': function(listener) {
			this.listeners.push(listener);
		},
	
		'removeListener': function(listener) {
			delete this.listeners[this.listeners.indexOf(listener)];
		},
	
		'notify': function(obj) {
			for(var i = 0; i < this.listeners.length; i++) {
				var listener = this.listeners[i];
				if (typeof listener !== 'undefined') {
					listener.process(obj);
				}
			}
		}
	}; // 发布者
	
	function Subscriber() {
	
	}
	Subscriber.prototype = {
		'process': function(obj) {
			console.log(obj);
		}
	};　// 订阅者
	
	
	var publisher = new Publisher();
	publisher.addListener(new Subscriber());
	publisher.addListener(new Subscriber());
	publisher.notify({name: 'michaelqin', ageo: 30}); // 发布一个对象到所有订阅者
	publisher.notify('2 subscribers will both perform process'); // 发布一个字符串到所有订阅者
	
	```

---

* **简述一下js的闭包？**
	
	闭包就是能够读取其他函数内部变量的函数。

	由于在Javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成"定义在一个函数内部的函数"。
	
	所以，在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

	```
	function f1(){
	   var n = 999;
	   function f2(){
	       alert(n);
	   }
	   return f2;
	}
	
	var result = f1();
	result();//999
	```
	f2()就是闭包

---

* **什么是垃圾回收机制**

	JavaScript 垃圾回收的机制很简单：找出不再使用的变量，然后释放掉其占用的内存，但是这个过程不是时时的，因为其开销比较大，所以垃圾回收器会按照固定的时间间隔周期性的执行。

---

* **列举数组相关的常用方法**

	> 下面这些方法会改变数组自身的值：
	
	1. ``Array.prototype.pop()`` 删除数组的最后一个元素，并返回这个元素
	2. ``Array.prototype.push()`` 在数组的末尾增加一个或多个元素，并返回数组长度
	3. ``Array.prototype.shift()`` 删除数组的第一个约束，并返回这个元素
	4. ``Array.prototype.unshift()`` 在数组的开头增加一个或多个元素，并返回数组长度
	5. ``Array.prototype.sort()`` 对数组进行排序，并返回当前数组。
	6. ``Array.prototype.reverse()`` 颠倒数组中元素的排列顺序。
	7. ``Array.prototype.splice()`` 在任意位置给数组添加或删除任意个元素。

	> 下面这些方法不会改变数组自身的值，只会返回一个新的数组或者其他值：
	
	1. ``Array.prototype.concat()`` 返回当前数组和其他若干数组或者若干个费数组值组合而成的新数组
	2. ``Array.prototype.includes()`` 判断当前数组是否包含某指定的值。
	3. ``Array.prototype.join()`` 连接所有数组元素组成一个字符串
	4. ``Array.prototype.slice()`` 抽取当前数组中的一段元素组合成一个新数组
	5. ``Array.prototype.toString()`` 返回由数组元素组成的字符串
	6. ``Array.prototype.indexOf()`` 返回数组中第一个与指定值相等的元素的索引，如果找不到返回-1
	7. ``Array.prototype.lastIndexOf()`` 返回数组中最后一个与指定值相等的元素的索引，如果找不到返回-1

	> 遍历方法:
	
	1. ``Array.prototype.forEach()`` 为数组中每一个元素执行一次回调。
	2. ``Array.prototype.every()`` 如果数组中每个元素都满足测试函数，则返回true，否则返回false；
	3. ``Array.prototype.some()`` 如果数组中至少有一个元素满足测试函数，则返回true，否则返回false
	4. ``Array.prototype.filter()`` 将所有在过滤函数中返回true的数组元素放假一个新数组中并返回。
	5. ``Array.prototype.find()`` 找到第一个满足测试函数的元素并返回那个元素的值，如果找不到，返回undefined
	6. ``Array.prototype.map()`` 返回一个由回调函数的返回值组成的新数组
	7. ``Array.prototype.reduce()`` 从左到右为每个元素执行一次回调函数，并把上次回调函数的返回值放在一个暂存器中传给下次回调函数，并返回最后一次回调函数的返回值。


---

* **列举字符串相关的常用方法**

	1. ``String.prototype.indexOf()`` 从字符串对象中返回首个被发现的给定值的索引，没找到返回-1
	2. ``String.prototype.lastIndexOf()`` 从字符串对象中返回最后一个被发现的给定值的索引，没找到返回-1
	3. ``String.prototype.charAt()`` 返回特定位置的字符
	4. ``String.prototype.split()`` 将字符串分割为数组
	5. ``String.prototype.match()`` 使用正则表达式
	6. ``String.prototype.slice()`` 摘取一个字符串区域，返回一个新字符串
	7. ``String.prototype.substring()`` 返回在字符串中指定两个下标之间的字符
	8. ``String.prototype.substr()`` 通过指定字符数返回在指定位置开始的字符串中的字符。
	9. ``String.prototype.trim()`` 从字符串的开始和结尾去除空格
	10. ``String.prototype.concat()`` 连接两个字符串，返回新字符串
	11. ``String.prototype.includes()`` 判断一个字符串是否包含某个字符

---

* **Math相关的常用方法**

	1. ``Math.abs(x)`` 返回绝对值
	2. ``Math.ceil(x)`` 返回x向上取整后的值
	3. ``Math.floor(x)`` 返回x向下取整后的值
	4. ``Math.min(x)``  返回最小值
	5. ``Math.max(x)`` 返回最大值
	6. ``Math.pow(x, y)`` 返回x的y次幂
	7. ``Math.random(x)`` 返回0-1之间的伪随机数
	8. ``Math.round(x)`` 返回四舍五入后的整数
	9. ``Math.sign(x)`` 返回x的符号函数，判断x是正数、负数还是0 

---

* **Object相关的常用方法**

	1. ``Object.assign()`` 通过复制一个或多个对象来创建一个新对象
	2. ``Object.create()`` 使用指定的原型对象和属性创建一个新对象
	3. ``Object.defineProperty()`` 给对象添加一个属性并指定该属性的配置
	4. ``Object.keys()`` 返回一个包含所有给定对象自身可枚举属性名称的数组
	5. ``Object.values()`` 返回给定对象自身可枚举值的数组

---

* **实现一个简单的命令行交互程序?**

	```
	var cp = require('child_process');

	var child = cp.spawn('echo', ['你好', "钩子"]); // 执行命令
	child.stdout.pipe(process.stdout); // child.stdout是输入流，process.stdout是输出流
	// 这句的意思是将子进程的输出作为当前程序的输入流，然后重定向到当前程序的标准输出，即控制台
	```
	
---

* **两个node程序之间怎样交互?**

	使用``child_process``的``fork``．原理是子程序用``process.on``, ``process.send``，父程序里用``child.on``,``child.send``进行交互.
	
	```
	1) fork-parent.js
	var cp = require('child_process');
	var child = cp.fork('./fork-child.js');
	child.on('message', function(msg){
		console.log('老爸从儿子接受到数据:', msg);
	});
	child.send('我是你爸爸，送关怀来了!');

	2) fork-child.js
	process.on('message', function(msg){
		console.log("儿子从老爸接收到的数据:", msg);
		process.send("我不要关怀，我要银民币！");
	});
	```
	
---

* **怎样让一个js文件变得像``linux``命令一样可执行?**

	1. 在``myCommand.js``文件头部加入 ``#!/usr/bin/env node`` 
	2. ``chmod``命令把js文件改为可执行即可 
	3. 进入文件目录，命令行输入``myComand``就是相当于``node myComand.js``了

---

* **``child-process``和``process``的``stdin,stdout,stderror``是一样的吗?**

	概念都是一样的，输入，输出，错误，都是流．区别是在父程序眼里，子程序的``stdout``是输入流，``stdin``是输出流．
	
---

* **node中的异步和同步怎么理解**

	node是单线程的，异步是通过一次次的循环事件队列来实现的．同步则是说阻塞式的IO,这在高并发环境会是一个很大的性能问题，所以同步一般只在基础框架的启动时使用，用来加载配置文件，初始化程序什么的．
	
---

* **有哪些方法可以进行异步流程的控制?**

	1. 多层嵌套回调 
	2. 为每一个回调写单独的函数，函数里边再回调 
	3. 用第三方框架比方``async, q, promise``等

---

* **怎样绑定node程序到80端口?**

	1. ``sudo`` 
	2. ``apache/nginx``代理 
	3. 用操作系统的``firewall iptables``进行端口重定向

---

* **有哪些方法可以让node程序遇到错误后自动重启?**

	1. ``runit`` 
	2. ``forever`` 
	3. ``nohup npm start &``
	4. ``docker``

---

* **怎样充分利用多个CPU?**

	一个CPU运行一个node实例
	
---

* **怎样调节node执行单元的内存大小?**

	用``--max-old-space-size`` 和 ``--max-new-space-size`` 来设置 v8 使用内存的上限
	
---

* **程序总是崩溃，怎样找出问题在哪里?**

	1. ``node --prof`` 查看哪些函数调用次数多 
	2. ``memwatch``和``heapdump``获得内存快照进行对比，查找内存溢出

---

* **有哪些常用方法可以防止程序崩溃?**

	1. ``try-catch-finally`` 
	2. ``EventEmitter/Stream error``事件处理 
	3. ``domain``统一控制 
	4. ``jshint``静态检查 
	5. ``jasmine/mocha``进行单元测试

---

* **怎样调试node程序?**

	 ``node --debug app.js`` 和``node-inspector``
	 
---

* **如何捕获NodeJS中的错误，有几种方法?**

	1. 监听错误事件``req.on('error', function(){})``, 适用``EventEmitter``存在的情况; 
	2. ``Promise.then.catch(error)``,适用``Promise``存在的情况 
	3. ``try-catch``,适用``async-await``和``js``运行时异常，比如``undefined object``

---

* **async都有哪些常用方法，分别是怎么用?**

	``async``是一个js类库，它的目的是解决js中异常流程难以控制的问题．``async``不仅适用在node.js里，浏览器中也可以使用．
	
	1、``async.parallel``并行执行完多个函数后，调用结束函数
	
	```
	async.parallel([
	    function(){ ... },
	    function(){ ... }
	], callback);
	```
	
	async.series串行执行完多个函数后，调用结束函数
	
	```
	async.series([
	    function(){ ... },
	    function(){ ... }
	]);
	```
	
	2、``async.waterfall``依次执行多个函数，后一个函数以前面函数的结果作为输入参数
	
	```
	async.waterfall([
	    function(callback) {
	        callback(null, 'one', 'two');
	    },
	    function(arg1, arg2, callback) {
	      // arg1 now equals 'one' and arg2 now equals 'two'
	        callback(null, 'three');
	    },
	    function(arg1, callback) {
	        // arg1 now equals 'three'
	        callback(null, 'done');
	    }
	], function (err, result) {
	    // result now equals 'done'
	});
	```
	
	3、``async.map``异步执行多个数组，返回结果数组
	
	```
	async.map(['file1','file2','file3'], fs.stat, function(err, results){
	    // results is now an array of stats for each file
	});
	```
	
	4、``async.filter``异步过滤多个数组，返回结果数组
	
	```
	async.filter(['file1','file2','file3'], fs.exists, function(results){
	    // results now equals an array of the existing files
	});
	```
	
---

* **express项目的目录大致是什么样子的**

	``app.js, package.json, bin/www, public, routes, views``
	
---

* **express常用函数**

	1. ``express.Router``路由组件
	2. ``app.get``路由定向
	3. ``app.configure``配置
	4. ``app.set``设定参数
	5. ``app.use``使用中间件

---

* **express中如何获取路由的参数**

	1. ``req.params``
	2. ``req.body``
	3. ``req.query``

---

* **express的response有哪些常用方法**

	1. res.download()	弹出文件下载
	2. res.end()	结束response
	3. res.json()	返回json
	4. res.jsonp()	返回jsonp
	5. res.redirect()	重定向请求
	6. res.render()	渲染模板
	7. res.send()	返回多种形式数据
	8. res.sendFile	返回文件
	9. res.sendStatus()	返回状态

----

* **实现以下函数，使得输入的字符串逆序输出**

	```
	function reverse(str) {
	  let res = str.split('');
	  return res.reverse().join('');
	}
	
	reverse('hello world!'); // output: '!dlrow olleh'
	```
	
	进阶问题

	```
	'hello world!'.reverse();  // output: '!dlrow olleh'
	```
	
	这个问题需要给String的原型上添加方法，但是要考虑有一些改造：

	```
	String.prototype.reverse = function reverse() {
	  let res = this.split('');
	  return res.reverse().join('');
	}
	```

---

* **实现sleep 函数**

	要求用法：
	
	```
	/**
	- 使当前运行的异步操作（promise 或者 async）停止等待若干秒
	- 
	- @param ms */
	(async () => {
	  console.log('hello')
	  await sleep(2000) // 等待两秒
	  console.log('world')
	})()
	```
	
	```
	const sleep = (ms) => {
	  new Promise(resolve, reject) {
	    setTimeOut(() => {
	      resolve();
	    }, ms)
	  }
	}

	```
	
---

* **实现throttle 节流函数**

	用法：
	
	```
	const throFun = () => console.log('hello');
	const thro = throttle(throFun, 300);
	document.body.onscroll = () => {
	  thro();  // 调用至少间隔 300 毫秒才会触发一次
	}
	```
	
	```
	/**
	- 频率控制，返回函数连续调用时，action 执行频率限定为 1次 / delay
	- @param delay  {number}    延迟时间，单位毫秒
	- @param action {function}  请求关联函数，实际应用需要调用的函数
	- @return {function}    返回客户调用函数 
	*/
	function throttle(action, delay) {
	  var previous = 0;
	  // 使用闭包返回一个函数并且用到闭包函数外面的变量previous
	  return function() {
	    var _this = this;
	    var args = arguments;
	    var now = new Date();
	    if(now - previous > delay) {
	        action.apply(_this, args);
	        previous = now;
	    }
	  }
	}
	```

---

* **实现debounce 防抖函数**

	用法：
	
	```
	const throFun = () => console.log('hello');
	const thro = debounce(throFun, 300);
	document.body.onscroll = () => {
	  thro();  // 若一直调用则不会执行，空闲时间大于 300 毫秒才会执行
	}
	```
	
	```
	/**
	- 空闲控制 返回函数连续调用时，空闲时间必须大于或等于 delay，action 才会执行
	- @param delay   {number}    空闲时间，单位毫秒
	- @param action {function}  请求关联函数，实际应用需要调用的函数
	- @return {function}    返回客户调用函数 */
	function debounce(action, delay) {
	  var timer; // 维护一个 timer
	  return function () {
	      var _this = this; // 取debounce执行作用域的this
	      var args = arguments;
	      if (timer) {
	          clearTimeout(timer);
	      }
	      timer = setTimeout(function () {
	          action.apply(_this, args); // 用apply指向调用debounce的对象，相当于_this.action(args);
	      }, delay);
	  };
	}
	```
	
---

* **数组去重有哪些方法？**

	```
	const arr = [1,2,3,4,4,3,2,1];
	// 方法一：new Set ES6
	return [...new Set(arr)]; // 注意...的用法
	
	// 方法二：双层for循环 (这是一种方法，但是性能不好)
	function unique(arr){
	  var res=[];
	  for (var i = 0; i < arr.length; i++) {
	    for (var j = i+1; j < arr.length; j++) {
	      if (arr[i] === arr[j]) {
	        ++ i;
	        j = i;
	      }
	    }
	    res.push(arr[i]);
	  }
	  return res;
	}
	
	// 方法三：单层for循环 + indexOf
	function unique(array){
	    var res = [];
	    for(var i = 0; i < array.length; i++) {
	        //如果当前数组的第i项在当前数组中第一次出现的位置是i，才存入数组；否则代表是重复的
	        if (array.indexOf(array[i]) === i) {
	            res.push(array[i])
	        }
	    }
	    return res;
	}
	// 方法三点三：或者这样
	function unique(array){
	    let res = [];
	    for(var i = 0; i < array.length; i++) {
	        if (res.indexOf(array[i]) === -1) {
	            res.push(array[i]);
	        }
	    }
	    return res;
	}
	
	// 方法四：如果可以容忍改变原有数组的情况下，这样性能更好
	function unique(array){
	    // 注意这里一定要倒叙for循环，否则会出现问题
	    for(var i = array.length - 1; i > 0; i--) { 
	        if (array.indexOf(array[i]) !== i) {
	            array.splice(i, 1);
	        }
	    }
	    // 因为少声明一个变量，节省了内存空间
	    return array;
	}

	```
	
---

* **exports 和 module.exports 的区别和关系 ?**

	用一句话来说明就是，require方能看到的只有``module.exports``这个对象，它是看不到``exports``对象的，而我们在编写模块时用到的``exports``对象实际上只是对``module.exports``的引用。
	
	1. module.exports 初始值为一个空对象 {}
	2. exports 是指向的 module.exports 的引用
	3. require() 返回的是 module.exports 而不是 exports

	所有的 ``exports`` 做的工作实际上是收集属性，如果 ``module.exports`` 当前没有任何属性，``exports``便将收集到的属性添加到 ``module.exports`` 上。如果 ``module.exports``已经存在若干属性，``exports`` 上的属性都会被忽略。
	
---

* **客户端禁用cookie后 怎么使用session**

	1. URL重写，就是把``session id``直接附加在URL路径的后面
	2. 表单隐藏字段。就是服务器会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把``session id``传递回服务器。

---

* **扁平化数组**

	完成一个函数,接受数组作为参数,数组元素为整数或者数组,数组元素包含整数或数组,函数返回扁平化后的数组
	
	如：[1, [2, [ [3, 4], 5], 6]] => [1, 2, 3, 4, 5, 6]
	
	```
	function flat(array, result = []) {
		for (let i = 0; i < array.length; i++) {
			const value = array[i];
			if (Array.isArray(value)) {
				flat(value, result);
			} else {
				result.push(value);
			}
		}
		return result;
	}
	```
	
	```
	let arr = [1, 2, [3, 4, 5, [6, 7], 8], 9, 10, [11, [12, 13]]]

	const flatten = function (arr) {
	    while (arr.some(item => Array.isArray(item))) {
	        arr = [].concat(...arr)
	    }
	    return arr
	}
	
	console.log(flatten(arr))
	```
	
	```
	const flattenDeep = (arr) => Array.isArray(arr)
  ? arr.reduce( (a, b) => [...a, ...flattenDeep(b)] , [])
  : [arr]
	
	flattenDeep([1, [[2], [3, [4]], 5]])
	```
	
	```
	Array.from(new Set(arr.toString().split(',').sort((a,b)=>{return a-b;}).map(Number)));
	```
	
	```
	Array.from(new Set(arr.flat(Infinity))).sort((a,b)=>{return a-b});
	```
---

* **遍历一个 javascript 对象**

	例如： ``obj = {name:'lucy',age:15}``
	
	```
		for(let item in obj){
			console.log(item, obj[item])
		}
	```
	
---

* **错误处理的常见方法有哪些 ?**

	1、 try catch 方式
	
	```
	try {
	    syncError()
	} catch (e) {
	    /*处理异常*/
	    console.log(e.message)
	}
	```
	2、 callback方式
	
	```
	fs.mkdir('/dir', function (e) {
	    if (e) {
	        /*处理异常*/
	        console.log(e.message)
	    } else {
	        console.log('创建目录成功')
	    }
	})
	```
	3、 event方式
	
	```
	let events = require("events");
	//创建一个事件监听对象
	let emitter = new events.EventEmitter();
	//监听error事件
	emitter.addListener("error", function (e) {
	    /*处理异常*/
	    console.log(e.message)
	});
	//触发error事件
	emitter.emit("error", new Error('出错啦'));
	```
	
	4、 Promise方式
	
	```
	new Promise((resolve, reject) => {
	    syncError()
	}).then(() => {
        //...
	}).catch((e) => {
        /*处理异常*/
        console.log(e.message)
    })
	```
	
	5、 Async/awiat方式
	
	Async/Await是基于Promise的，所以Promise无法捕获的异常，Async/Await同样无法捕获
	
	6、 process方式
	
	process方式可以捕获任何异常(不管是同步代码块中的异常还是异步代码块中的异常)

	```
	process.on('uncaughtException', function (e) {
	    /*处理异常*/
	    console.log(e.message)
	});
	asyncError()
	syncError()
	```
	
	7、domain方式
	
	process方式虽然可以捕获任何类型的异常，但是process太过笨重，除了记录下错误信息，其他地方不适合使用，domain这个也可以处理任何类型异常的模块，显然是一个不错的选择。

	```
	let domain = require('domain')
	let d = domain.create()
	d.on('error', function (e) {
	    /*处理异常*/
	    console.log(e.message)
	})
	d.run(asyncError)
	d.run(syncError)
	```

---

* **Libuv 是什么, 他跟 Node 是什么关系 ?**

	libuv是一个高性能的，事件驱动的I/O库，并且提供了跨平台（如windows, linux）的API。libuv强制使用异步的，事件驱动的编程风格。它的核心工作是提供一个event-loop，还有基于I/O和其它事件通知的回调函数。

	nodejs使用libuv监听各种异步事件

---

* **Node.js 适合用于什么开发, 为什么 ?**

	Node.js适合做一些高并发的，I/O密集型的应用。
	当应用程序需要处理大量并发的I/O，而在向客户端发出响应之前，应用程序内部并不需要进行非常复杂的处理的时候，Node.js非常适合。Node.js也非常适合与web socket配合，开发长连接的实时交互应用程序。
	
---

* **Node.js 优点(特性), 缺点, 以及如何弥补 ?**

	> Node.js优点：
	
	1、采用事件驱动、异步编程，为网络服务而设计。其实Javascript的匿名函数和闭包特性非常适合事件驱动、异步编程。而且JavaScript也简单易学，很多前端设计人员可以很快上手做后端设计。
	
	2、Node.js非阻塞模式的IO处理给Node.js带来在相对低系统资源耗用下的高性能与出众的负载能力，非常适合用作依赖其它IO资源的中间层服务。
	
	3、Node.js轻量高效，可以认为是数据密集型分布式部署环境下的实时应用系统的完美解决方案。Node非常适合如下情况：在响应客户端之前，您预计可能有很高的流量，但所需的服务器端逻辑和处理不一定很多。
	
	4、单进程，节约资源
	
	5、异步I/O，提升并发量
	
	> Node.js缺点：
	
	1、可靠性低, 一旦出现未捕获的异常将直接导致服务不可用.
	
	2、单进程，单线程，只支持单核CPU，不能充分的利用多核CPU服务器。一旦这个进程崩掉，那么整个web服务就崩掉了。
	
	不过以上缺点可以可以通过代码的健壮性来弥补。使用cluster模式开启多个进程。
	设置全局异常捕获器。
	
---

* **事件循环的机制是怎样的 ?**

	1. timers: 这个阶段执行定时器队列中的回调如 setTimeout() 和 setInterval()。
	2. I/O callbacks: 这个阶段执行几乎所有的回调。但是不包括close事件，定时器和setImmediate()的回调。
	3. idle, prepare: 这个阶段仅在内部使用，可以不必理会。
	4. poll: 等待新的I/O事件，node在一些特殊情况下会阻塞在这里。
	5. check: setImmediate()的回调会在这个阶段执行。
	6. close callbacks: 例如socket.on('close', ...)这种close事件的回调。

---

* **mongodb有哪些常用优化措施**

	索引和分区
	
---

* **mongoose是什么？有支持哪些特性?**

	``mongoose``是``mongodb``的文档映射模型．主要由``Schema``, `Model`和`Instance`三个方面组成．`Schema`就是定义数据类型，`Model`就是把`Schema`和js类绑定到一起，`Instance`就是一个对象实例．常见`mongoose`操作有,`save, update, find. findOne, findById, static`方法等


---

* **如何保证缓存与数据库双写时的数据一致性？**

	你只要用缓存，就可能会涉及到缓存与数据库双存储双写，你只要是双写，就一定会有数据一致性的问题，那么你如何解决一致性问题？
	
	一般来说，就是如果你的系统不是严格要求缓存+数据库必须一致性的话，缓存可以稍微的跟数据库偶尔有不一致的情况，最好不要做这个方案，读请求和写请求串行化，串到一个内存队列里去，这样就可以保证一定不会出现不一致的情况
	
	串行化之后，就会导致系统的吞吐量会大幅度的降低，用比正常情况下多几倍的机器去支撑线上的一个请求。

---

* **http的状态码中，499是什么？如何出现499，如何排查跟解决**

	499对应的是 `“client has closed connection”`，客户端请求等待链接已经关闭，这很有可能是因为服务器端处理的时间过长，客户端等得“不耐烦”了。还有一种原因是两次提交post过快就会出现499。
解决方法：

	* 前端将timeout最大等待时间设置大一些
	* nginx上配置`proxy_ignore_client_abort on`;

---

* **new操作符都做了什么**

	四大步骤：
	1. 创建一个空对象，并且 this 变量引用该对象，// `let target = {}`;
	2. 继承了函数的原型。// `target.proto = func.prototype;`
	3. 属性和方法被加入到 this 引用的对象中。并执行了该函数func// `func.call(target);`
	4. 新创建的对象由 this 所引用，并且最后隐式的返回 this 。// 如果`func.call(target)`返回的res是个对象或者function 就返回它
	
	```
	function new(func) {
		lat target = {};
		target.__proto__ = func.prototype;
		let res = func.call(target);
		if (typeof(res) == "object" || typeof(res) == "function") {
			return res;
		}
		return target;
	}
	```
	
	```
	function new(fn, ...arg){
		const obj = Object.create(fn.prototype);
		fn.apply(obj, arg);
		return obj;
	}
	```
	
---

* **手写一个JS深拷贝**

	1、乞丐版
	
	```
	consr netObj = JSON.parse(JSON.stringify(oldObj));
	```
	
	2、升级版
	
	```javascript
	function cloneDeep(obj){
		let ret;
		if(typeof obj === 'object'){
			ret = obj.constructor === Array ? [] : {};
			for (let i in obj){
				ret[i] = typeof obj[i] === 'object' ? cloneDeep(obj[i]) : obj[i];
			}
		} else {
			ret = obj;
		}
		return obj;
	}
	```
	
---

* **手写一个instanceof**

	```
	function _instanceof(left, right){
		let proto = left.__proto__;
		const prototype = right.prototype;
		while(true){
			if(proto===null) return false;
			if(proto===prototype) return true;
			proto = proto.__proto__;
		}
	}
	```
	
---

* **实现一个JSON.stringify**

	* Boolean | Number| String 类型会自动转换成对应的原始值。
	* undefined、任意函数以及symbol，会被忽略（出现在非数组对象的属性值中时），或者被转换成 null（出现在数组中时）。
	* 不可枚举的属性会被忽略
	* 如果一个对象的属性值通过某种间接的方式指回该对象本身，即循环引用，属性也会被忽略。

	```
	function jsonStringify(obj) {
	    let type = typeof obj;
	    if (type !== "object") {
	        if (/string|undefined|function/.test(type)) {
	            obj = '"' + obj + '"';
	        }
	        return String(obj);
	    } else {
	        let json = []
	        let arr = Array.isArray(obj)
	        for (let k in obj) {
	            let v = obj[k];
	            let type = typeof v;
	            if (/string|undefined|function/.test(type)) {
	                v = '"' + v + '"';
	            } else if (type === "object") {
	                v = jsonStringify(v);
	            }
	            json.push((arr ? "" : '"' + k + '":') + String(v));
	        }
	        return (arr ? "[" : "{") + String(json) + (arr ? "]" : "}")
	    }
	}
	jsonStringify({x : 5}) // "{"x":5}"
	jsonStringify([1, "false", false]) // "[1,"false",false]"
	jsonStringify({b: undefined}) // "{"b":"undefined"}"
	```

---
