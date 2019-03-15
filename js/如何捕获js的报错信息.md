# 如何捕获js的报错信息

## window.onerror

```js
  window.onerror = function (errorMessage, scriptURI, lineNumber, columnNumber, error) {
    console.log(errorMessage)
    console.log(scriptURI)
    console.log(lineNumber)
    console.log(columnNumber)
    console.log(erro)
  }
```
由于同源策略，存放在第三方（cdn）服务器上js报错信息，获取不到。
解决办法：
1. 添加crossorigin="anonymous" 到script标签
```js
    <script src="https://xxx.com/xxx.js" crossorigin="anonymous"></script>
```

2. 添加支持跨域访问的设置

```json
   Access-Control-Allow-Origin: *
```
## try catch

## react 
 As of React 16, errors that were not caught by any error boundary will result in unmounting of the whole React component tree.
## vue
Vue提供了一个全局配置errorHandler，用于收集Vue运行时发生的错误。
```js
   Vue.config.errorHandler = function (err, vm, info) {
     // handle error
     //`err`是js错误栈信息，可以获取到具体的js报错位置。
     //`vm` vue实例
     //`info` 是 Vue 特定的错误信息，比如错误所在的生命周期钩子
     // 只在 2.2.0+ 可用
   }
```
## 小程序
```js
App({
  onError (msg) {
    console.log(msg);//msg就是报错信息
  }
})
```
## node