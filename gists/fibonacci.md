实现斐波那契数列的方式：
1. 利用generator和解构
```js
function* fibonacci() {
  let [prev, next] = [0, 1];
  for (;;) {
    yield curr;
    [prev, curr] = [curr, prev + curr];
  }
}

for (let n of fibonacci()) {
  if (n > 1000) break;
  console.log(n);
}
```