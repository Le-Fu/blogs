### 模块机制
在开发的项目体量越来越大，命名空间污染变成为一个令人头疼的问题。 开发者们，就提出了模块化开发的思想，形成封闭的作用域，便不会再影响到别人写的代码，每个开发者都只用专注开发自己负责的部分代码，留好模块的公共API就行。
模块模式具备两个必要条件：
1 必须有外部的封闭函数，该函数必须至少被调用一次（每次调用都会创建一个新的模块实例）。
2 封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。
下面是一段比较典型的实现模块的方法-模块暴露:
```
 var foo = (function Moudle() {
	var something = 'cool';
	var another = [1,2,3];

	function doSomething() {
		console.log( something );
	}
	
	function doAnother() {
		console.log( another.join( "!" ) );
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	}
})();

foo.doSomething();	//cool
foo.doAnother(); 	//1!2!3 
``` 

上面代码利用了IIFE将返回值直接赋值给单例的模块实例标识符foo。

###  现代的模块机制

下面是一个模块依赖加载器:

``` 
var MyModules = (function Manager() {
	var modules = {};

	function define(name, deps, impl) {
		for (var i=0; i < deps.length; i++) {
			deps[i] = modules[deps[i]];
		}
		modules[name] = impl.apply( impl, deps );
	}

	function get(name) {
		return modules[name];
	}

	return {
		define: define,
		get: get
	};

})(); 
```
使用方法如下: 
```
MyMoudules.define("bar", [], function() {
	function hello(who) {
		return "Let's introduce: " + who;
	}
	
	return { 
		hello: hello
	}
});
MyModules.define("foo", ["bar"], function () {
	var name = "Simon";

	function awesome() {
		console.log(bar.hello(name).toUppercase());
	}
	
	return {
		awesome: awesome
	}

});
var bar = MyModules.get('bar');
var foo = MyModules.get('foo');
bar.hello('Simon');		//Let's introduce: Simon
foo.awesome();			//LET'S INTRODUCE: SIMON 
```

`foo`和`bar`模块都是通过一个返回公共API的函数来定义的。 `foo`甚至接受`bar`的实例作为依赖参数，并能相应的使用它。

### 未来模块机制

ES6中为模块增加了一级语法支持。在通过模块系统进行加载使，ES6会将文件当作独立的模块来处理。每个模块可以导入其他模块或特定的API成员，同样也可以导出自己的API成员。ES6的模块没有行内格式，必须被定义在独立的文件中（一个文件一个模块）。浏览器或引擎有一个默认的“模块加载器”可以再倒入模块时同步地加载模块文件。

bar.js
```
	function hello(who) {
		return "Let's introduce: " + who;
	}
	
	export hello
```
foo.js
```
	import hello from "bar"；
		
	var name = "Simon";
	
	function awesome() {
		console.log(bar.hello(name).toUppercase());
	}

	export  awesome;
```
baz.js
```	
	mudule foo from "foo";
	module bar from "bar";
	
	console.log(
		bar.hello(" rhion ");
	);	//Let's introduce: rhion
	
	foo.awesome(); 	//LET'S INTRODUCE: SIMON 
```