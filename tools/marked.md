这篇文章，介绍如何在自己的网站中使用markdown使自己的文章排版优雅。
### 资源引入
首先，我们知道markdown是一种标记语言，功能和HTML是一样的，HTML解析有浏览器渲染内核，那么markdown要想解析就的靠引入专门的解析器。这里我选用了marked.js。另外，做程序员的小朋友们可能需要展示代码，只用marked.js并不能使代码高亮，所以还需要引入highlight.js，来使代码更好看。
代码如下：
```
&lt;link rel="stylesheet" href="https://cdn.bootcss.com/highlight.js/9.9.0/styles/magula.min.css"&gt;
&lt;script src="https://cdn.bootcss.com/marked/0.3.6/marked.min.js" &gt;&lt;/script &gt;
&lt;script src="https://cdn.bootcss.com/highlight.js/9.9.0/highlight.min.js" &gt;&lt;/script &gt;

```
外部资源加载完毕。
### HTML结构配合
然后，你要解析的md文本在哪里呢，一般是通过后台数据库传到前台来的。我已PHP为html呈现如下：
```
 &lt;div id="preview"&gt;&lt;div class="content" style="display: none;"&gt;&lt;?php echo $blog-&gt;content;?&gt;&lt;/div&gt;&lt;/div&gt;
```
### JS的调用，开始解析
最后呢，就是markdown的解析器是js文件的调用，代码如下：
```
    $(function(){
        //markdown
        var myblog = new marked.Renderer();
        marked.setOptions({
            renderer: myblog,
            gfm: true,
            tables: true,
            breaks: true,
            pedantic: false,
            sanitize: true,
            smartLists: true,
            smartypants: false
        });

        var $container = $('#preview');
        var $content = $container.text();
        var $preview = marked($content);
        hljs.initHighlightingOnLoad();//语法高亮
        $container.append($preview);
    });
```
这样，基本就可以用了，你正在看的这篇博客就是markdown写出来的。不过值得注意的是，`&lt;script&gt;`要想正确显示，需要写成&lt;script&gt;。
### 结语
按照上面的步骤，你的markdown应该就可以正常显示了。这是我在自己网站搭建中摸索出来的方式，可能会有其它更好的方式去解析markdown，欢迎提出宝贵建议，大家共同学习，共同进步。
最后，推荐:
语法参考
> http://wowubuntu.com/markdown/
