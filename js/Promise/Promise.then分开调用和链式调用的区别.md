```js
    var promise = new Promise(func)

    // 1
    promise.then(callback1)
    promise.then(callback2)
    // 2
    promise.then(callback1).then(callback2)
```
请问1和2一样吗？如果不一样，区别在哪

1的then都绑定在promise上，他们的拿到的值，都是func执行resolve时提供的值
2的第二个then，绑定在第一个then返回的promise上，他拿到的值，是callback1的返回值
如果callback1返回一个promise，第二个then的执行时机，将由该promise决定