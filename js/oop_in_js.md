```js
(function(){
	//私有静态成员
	var user = "";
	
	//私有静态方法
	function privateStaticMethod(){
	}

	
	Box = function(value){
		//私有成员
		var privateStaticUser = value; 
		
		//这个是私有方法
		function privateMethod(){
		}

		
		//公有方法，因为能访问私有成员，也可以说是特权函数，也可以说是实例方法
		this.getUser = function(){
			return user;
		};		
		
		//公有成员
		this.user = 1;
	};
	
	//公有共享访问
	Box.prototype.sharedMethod = function () {};
	
	//公有共享属性
	Box.prototype.sharedProperty = function () {};

	
	//公有静态方法 
	Box.staticMethod = function(){};
	
	//公有静态成员
	Box.staticProperty = 1; 
})();
```
> 摘自：[一段代码详解JavaScript面向对象](http://yanhaijing.com/javascript/2014/05/15/a-code-explain-javascript-oop/).