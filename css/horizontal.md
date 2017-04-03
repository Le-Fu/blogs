inline 或 inline-* 的元素【比如文本（text）或链接（link）】            
当它们的父元素是块级元素时，你可以水平居中行级元素，方法如下：
```
.center-children {
	 text-align: center;
}
```
这样会在inline，inline-block，inline-table，inline-flex等时起作用。

当他是块级元素时
	 你可以通过给一个块级元素设置margin-left和margin-right为auto（且它需要有一个设置的宽度，否则它占满整行，根本就没必要居中了）。示例代码通常是这样的：
```
.center-me {
	margin: 0 auto;
}
```
不管它的宽度或他的父元素的宽度是多少，你都能通过这种方式将它水平居中。

如果有两个或两个以上的块级元素需要在同一行内水平居中，你或许要让它们拥有不同的display属性。下面会举两个例子，一个是使他们display设为inline-block，另一种是flexbox：
```html
&lt;main class="inline-block-center"&gt;
  &lt;div&gt;
    I'm an element that is block-like with my siblings and we're centered in a row.
  &lt;/div&gt;
  &lt;div&gt;
    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
  &lt;/div&gt;
  &lt;div&gt;
    I'm an element that is block-like with my siblings and we're centered in a row.
  &lt;/div&gt;
&lt;/main&gt;

&lt;main class="flex-center"&gt;
  &lt;div&gt;
    I'm an element that is block-like with my siblings and we're centered in a row.
  &lt;/div&gt;
  &lt;div&gt;
    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
  &lt;/div&gt;
  &lt;div&gt;
    I'm an element that is block-like with my siblings and we're centered in a row.
  &lt;/div&gt;
&lt;/main&gt;
```

```
css:
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  margin: 20px 0;
  padding: 10px;
}

main div {
  background: black;
  color: white;
  padding: 15px;
  max-width: 125px;
  margin: 5px;
}

.inline-block-center {
  text-align: center;
}
.inline-block-center div {
  display: inline-block;
  text-align: left;
}

.flex-center {
  display: flex;
  justify-content: center;
}
```

除非你的意思是你有多个块级元素堆叠在彼此之上，这种情况下auto margin方法还是会有很好的效果：
```html
&lt;main&gt;
  &lt;div&gt;
    I'm an element that is block-like with my siblings and we're centered in a row.
  &lt;/div&gt;
  &lt;div&gt;
    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
  &lt;/div&gt;
  &lt;div&gt;
    I'm an element that is block-like with my siblings and we're centered in a row.
  &lt;/div&gt;
&lt;/main&gt;
```

```
css:
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  margin: 20px 0;
  padding: 10px;
}

main div {
  background: black;
  margin: 0 auto;
  color: white;
  padding: 15px;
  margin: 5px auto;
}

main div:nth-child(1) {
  width: 200px;
}
main div:nth-child(2) {
  width: 400px;
}
main div:nth-child(3) {
  width: 125px;
}
```

其实还有一种方法，就是给这几个元素加一个父元素，给父元素设置宽然后利用第二种方法来居中。
接下来，会写垂直居中，敬请期待。^o^
