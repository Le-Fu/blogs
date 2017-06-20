### 早期网页开发的交互
在早期的网页开发中，我们做交互时对dom进行事件绑定，是这样做的：

```html
&lt;div onclick="alert('Are you ok?')"&gt;&lt;/div&gt;
```

这样做的缺点很明显，高耦合，一旦项目大了，代码多了，自己去改都挺费劲儿的，更别说让别人维护你的代码了。
### 视图与行为分离
人们提倡视图与行为分离，下面的事件绑定方式诞生了：

```html
&lt;div id="div1"&gt;&lt;/div&gt;
```

```js
var oDiv = document.getElementById('div1');
oDiv.onclick = function () {
	alert('Are you ok?');
}
```

但是，这样还是存在一些问题：
每个事件只能注册一个函数

```js
var oDiv = document.getElementById('div1');
oDiv.onclick = fn1;
oDiv.onclick = fn2;

function fn1() {
	alert("我被覆盖了！");
}

function fn2() {
	alert("只有我被执行了！");
}
```

 - 解决办法一

```js
oDiv.onclick = function () {
	fn1();
	fn2();
}
```

但这样还是有缺陷：
		1）需要将所有一次添加进去，不能运行时添加；
		2）在事件处理函数中this将指向window,而不是oDiv；
		
 - 解决办法二

```js
/**
* 给一个DOM元素绑定事件
* @param {Function} fn 是事件处理函数
* @param {String} eventType 是事件名字，不包含on前缀，因为每个都有on，所以写个on是多余的
* @param {Element} obj 是要添加事件的HTML元素对象
*/
function addEvent(fn, eventType, obj) {
	var oldFn;
	if (obj["on" + eventType] instanceof Function) {
		oldFn = obj["on" + eventType]; // 当添加函数时，如果已注册过了，则将其保存起来
	}
	obj["on"+evtype] = function () {
		if (oldFn) {
			oldFn.call(this);
		}
		fn.call(this); // 使用call方法，使事件处理函数中的this仍指向obj
	};
}
```

这样已经解决了问题，但如何删除事件呢？如果直接将对象的oneventType这类的属性赋值为null将会删除所有的事件处理函数！
- 解决方法二改进版本
先将事件存储起来，存储在对象的__EventHandles属性里面

```js
eventHandlerCounter = 1; // 计数器，将统计所有添加进去的函数的个数,0位预留作其它用
function addEvent(fn, eventType, obj) {
	if (!fn._eventID) { // _EventID是给函数加的一个标识，见下面给函数添加标识的部分
		fn._eventID = eventHandlerCounter++; // 使用一个自动增长的计数器作为函数的标识以保证不会重复
	}
	if (!obj._eventHandlers) {
		obj._eventHandlers=[]; // 当不存在，也就是第一次执行时，创建一个，并且是数组
	}
	if (!obj._eventHandlers[eventType]) { // 将所有事件处理函数按事件类型分类存放
		obj._eventHandlers[eventType] = []; // 当不存在时也创建一个数组
		if (obj["on" + eventType] instanceof Function) { // 查看是否已经注册过其它函数
			obj._eventHandlers[eventType][0] = obj["on" + eventType];
			// 如果已经注册过，则将以前的事件处理函数添加到数组下标为0的预留的位置
			obj["on" + eventType] = handleEvents; // 使用handleEvents集中处理所有的函数
		}
	}
	obj._eventHandlers[eventType][fn._eventID] = fn; 
	// 如果函数是第一次注册为事件处理函数，那么它将被添加到数组中，函数的标识作为下标
	// 如果函数已经注册过相同对象的相同事件了，那么将覆盖原来的而不会被添加两次
	function handleEvents() {
		var fns = obj._eventHandlers[eventType];
		for (var i=0; i < fns.length; i++) {
			fns[i].call(this);
		}
	}
}
```

使用上面的函数已经可以在一个对象添加多个事件处理函数，在函数内部this关键字也指向了相应的对象，并且这些函数都被作了标识，那么移除某个事件处理函数就是轻而易举的了！

```js
// 使用传统方法：obj.onevtype = null;但这样会移除所有的事件处理函数
function delEvent(fn, eventType, obj) {
	if (!obj._eventHandlers || !obj._eventHandlers[eventType] || !fn._eventID) {
		return false;
	}
	if (obj._eventHandler[eventType][fn._eventID] == fn) {
		delete obj._eventHandler[eventType][fn._eventID];
	}
}
```

### 现代事件绑定方法

现代事件绑定方法有效的避免了传统事件绑定有许多缺陷，比如不能在一个对象的相同事件上注册多个事件处理函数。但W3C（国际标准）和IE的实现各不相同，所以要做兼容还需简单封装一下

- W3C

`obj.addEventListener(eventType,fn,useCapture)`
W3C提供的添加事件处理函数的方法。obj是要添加事件的对象，eventType是事件类型，不带on前缀，fn是事件处理函数，如果useCapture是true，则事件处理函数在捕获阶段被执行，否则在冒泡阶段执行

`obj.removeEventListener(eventType,fn,useCapture)`
W3C提供的删除事件处理函数的方法

- 微软IE方法

`obj.attachEvent(eventType,fn)`
IE提供的添加事件处理函数的方法。obj是要添加事件的对象，eventType是事件类型，带on前缀，fn是事件处理函数，IE不支持事件捕获

`obj.detachEvent(eventType,fn,)`
IE提供的删除事件处理函数的方法，eventType包含on前缀

```js
function addEvent(obj, eventType, fn, useCapture) {
	if (obj.addEventListener) {
		obj.addEventListener(eventType, fn, useCapture);
	} else {
		obj.attachEvent("on" + eventType, function() {
			fn.call(obj);
		});
	} else {
		obj["on" + eventType] = fn; //事实上这种情况不会存在
	}
}

function delEvent(obj, eventType, fn, useCapture) {
	if (obj.removeEventListener) {
		obj.removeEventListener(eventType, fn, useCapture);
	} else {
		obj.detachEvent("on" + eventType, fn);
	} else {
		obj["on" + eventType] = null;
	}
}
```

IE的attachEvent方法有一个问题，同一个函数可以被注册到同一个对象同一个事件上多次，解决方法：抛弃IE的attachEvent方法吧！IE下的attachEvent方法不支持捕获，和传统事件注册没多大区别(除了能绑定多个事件处理函数)，并且IE的attachEvent方法存在内存泄漏问题！

### ES6的事件绑定

当我们不考虑兼容性问题时，那就酸爽了。

```js
function handleEvent (eventName, {onElement, withCallback, useCapture = false} = {}, thisArg) {
  const element = onElement || document.documentElement

  function handler (event) {
    if (typeof withCallback === 'function') {
      withCallback.call(thisArg, event)
    }
  }

  handler.destroy = function () {
    return element.removeEventListener(eventName, handler, useCapture)
  }

  element.addEventListener(eventName, handler, useCapture)
  return handler
}
```

这样就显得特别优雅了。

```js
// 绑定事件
const handleClick = handleEvent('click', {
  onElement: element,
  withCallback: (event) => {
    console.log('Tada!')
  }
})

// 移除事件
handleClick.destroy()
```
It's over!    ^0^