### Yahoo性能工程师 Nicole Sullivan 在文章[《Reflows & Repaints: CSS Performance making your JavaScript slow?》](http://www.stubbornella.org/content/2009/03/27/reflows-repaints-css-performance-making-your-javascript-slow/)中总结了导致 reflow 发生的一些因素：

1.调整窗口大小（Resizing the window）
2.改变字体（Changing the font）
3.增加或者移除样式表（Adding or removing a stylesheet）
4.内容变化，比如用户在input框中输入文字（Content changes, such as a user typing text in an input box）
5.激活 CSS 伪类，比如 :hover (IE 中为兄弟结点伪类的激活)（Activation of CSS pseudo classes such as :hover (in IE the activation of the pseudo lass of a sibling)）
6.操作 class 属性（Manipulating the class attribute）
7.脚本操作 DOM（A script manipulating the DOM）
8.计算 offsetWidth 和 offsetHeight 属性（Calculating offsetWidth and offsetHeight）
9.设置 style 属性的值 （Setting a property of the style attribute）
### reflow 会引起开销，影响页面的性能，那如何才能做到合理的优化呢？Nicole Sullivan 也提供了部分建议：
1.如果想设定元素的样式，通过改变元素的 class 名 (尽可能在 DOM 树的最里层)（Change classes on the element you wish to style (as low in the dom tree as possible)）
2.避免设置多项内联样式（Avoid setting multiple inline styles）
3.应用元素的动画，使用 position 属性的 fixed 值或 absolute 值（Apply animations to elements that are position fixed or absolute）
4.权衡平滑和速度（Trade smoothness for speed）
5.避免使用 table 布局（Avoid tables for layout）
6.避免使用CSS的 JavaScript 表达式 (仅 IE 浏览器)（Avoid JavaScript expressions in the CSS (IE only)）