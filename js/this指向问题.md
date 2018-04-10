# this的指向
js中this的指向，在不同的情况下，是不一样的，总是让人迷惑。
我们可以归类如下：
### 暴露在全局的this
在浏览器环境中`this` -> `window`对象; 在node中，`this`->`global`对象;
> 以下皆以`全局对象`表述
### 普通函数调用
```js
function foo() {
    console.log(this);
}

foo(); // 全局对象
```
### 做为对象的方法调用
```js
var obj = {
    name: 'simon',
    method: function() {
        console.log(this);
    },
    me: this
}

obj.method(); // 这里this指向obj
console.log(obj.me); // 这里me指向全局对象
```
但是，obj.method被复制给一个变量，再去调用，`this`指向会改变：
```js
var objMethod = obj.method;

objMethod(); // 这里的this又指向了全局对象
```
### 调用构造函数
```js
function Foo(name) {
    this.name = name;
}

var newObj = new Foo(); // 这里的this指向新创建的对象newObj
```
### `call`、`apply`和`bind`显示设置this
```js
function bar(x, y) {
    console.log(this.a + x + y);
}

var obj = {a: 1, length: 1};

// 如下三个函数调用，this都指向了obj

bar.call(obj, 10, 20); // 31

bar.apply(obj, [10, 20]); // 31

bar.bind(obj, 10, 20)(); // 31

```
⚠️ 当使用 Function.prototype 上的 call 或者 apply 方法时，函数内的 this 将会被 显式设置为函数调用的第一个参数。

### ES6里面的箭头函数
```js
var obj = {
    name: 'simon',
    method: function() {
        function test() {
            console.log(this); 
        }
        test(); // this指向全局对象
    }
};
```
修改this指向方法：
```js
var obj = {
    name: 'simon',
    method: function() {
        var that = this;
        function test() {
            console.log(that); 
        }
        test(); // that指向obj
    }
};
```
也可以用箭头函数：
```js
const obj = {
    name: 'simon',
    method: function() {
        let test = () => {
            console.log(this); 
        }
        test(); // this指向obj
    }
};
```
箭头函数，this指向固定化，并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外层代码块的this。正是因为它没有this，所以也就不能用作构造函数

### 实例
```js
1.  Array.prototype.sortNum = function () {
        return this.sort(function(a, b){
            return a>b;
        })     
    }    
    // 这里this的指向？
2.  function foo() {
        return function () {
            return function () {
                return function () {
                    console.log(this.id);
                }
            }
        }   
    }
    var f = foo.call({id: 1});

    f()()(); // ?
    f.call({id: 1})()() // ?
    f().call({id: 2})() // ?
    f()().call({id: 3}) // ?
    
3.  var user = {
        name: 'dk',
        methond: function() {
            console.log(this.name);	
        }
    };
    function foo(fn) {
        fn();
    }
    foo(user.methond); // ?
```
