&nbsp;&nbsp;CSS的加载会阻塞DOM的渲染和后续js的执行。为了标记CSS已经加载完了,可以在紧随CSS引用之后写如下代码。	
```
&lt;script&gt;performance.mark('css:unblock'); &lt;/script &gt;
<!-- End CSS -->
```
&nbsp;&nbsp;下面的工具函数测量并返回你做的标记`mark`(即如上代码)到`Navigation Timing API`创造的`responseEnd`标记的时间。
```
function measureDuration(mark, opt_reference) {
  var reference = opt_reference || 'responseEnd';
  var name = reference + ':' + mark;

  // Clears any existing measurements with the same name.
  performance.clearMeasures(name);

  // Creates a new measurement from the reference point to the specified mark.
  // If more than one mark with this name exists, the most recent one is used.
  performance.measure(name, reference, mark);

  // Gets the value of the measurement just created.
  var measure = performance.getEntriesByName(name)[0];

  // Returns the measure duration.
  return measure.duration;
}
```
&nbsp;&nbsp;确保如上代码不会阻塞到其他代码执行，你需要写成这样子：
```
window.onload = function() {
  measureCssUnblockTime();
};


/**
 * Calculates the time duration between the responseEnd timing event and when
 * the CSS stops blocking rendering, then logs that value to the console.
 */
function measureCssUnblockTime() {
  console.log('CSS', 'unblock', measureDuration('css:unblock'));
}


/**
 * Accepts a mark name and an optional reference point in the navigation timing
 * API and returns the time duration between the reference point and the last
 * mark (chronologically).
 * @param {string} mark The mark name.
 * @param {string=} opt_reference An optional reference point from the
 *     navigation timing API. Defaults to 'responseEnd'.
 * @return {number} The time duration
 */
function measureDuration(mark, opt_reference) {
  var reference = opt_reference || 'responseEnd';
  var name = reference + ':' + mark;

  // Clears any existing measurements with the same name.
  performance.clearMeasures(name);

  // Creates a new measurement from the reference point to the specified mark.
  // If more than one mark with this name exists, the most recent one is used.
  performance.measure(name, reference, mark);

  // Gets the value of the measurement just created.
  var measure = performance.getEntriesByName(name)[0];

  // Returns the measure duration.
  return measure.duration;
}
```
&nbsp;&nbsp;你最好将上面的工具函数封装的一个叫`perf-analytics.js`的文件中，并将其在你主要文档的末尾引入。
```
< !-- Start performance analytics -->
&lt;script async src="perf-analytics.js" &gt;&lt;/script &gt;
< !-- End performance analytics -->
```
![css unblock time](img/performance/css-unblock.png)


