> call() 方法使用一个指定的 this 值和单独给出的一个或多个参数来调用一个函数

```js
Function.prototype.callFn = function(thisArg){
    thisArg = thisArg ? Object(thisArg): window;
    thisArg.fn = this;
    let args = [].slice.call(arguments, 1);
    let result = thisArg.fn(args);
    delete thisArg.fn;
    return result;
}
```