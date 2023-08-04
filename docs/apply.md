> apply() 方法调用一个具有给定 this 值的函数，以及一个数组(或一个类数组对象)的形式提供的参数。

对于一些需要写循环以遍历数组各项的需求，我们可以用 apply 完成以避免循环。以下示例，我们将用 Math.max/Math.min 求得数组中的最大/小值。
```js
const numbers = [5, 6, 2, 3, 7];
let max = Math.max.apply(null, numbers);//等同于Math.max(5,6,2,3,7)
let min = Math.min.apply(null, numbers);
```


**实现apply**
```js
Function.prototyp.applyFn = function(thisArg, argsArray){
    if(typeof argsArray === 'undefined' || argsArray === null){
        argsArray = []
    }

    thisArg = thisArg ? Object(thisArg): window;
    thisArg.fn = this;
    var result = thisArg.fn(...argsArray);
    delete thisArg.fn;
    return result;
}
```