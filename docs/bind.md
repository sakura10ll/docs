先看下 bind 是什么

```js
var obj = {};
console.log(typeof Function.prototype.bind)  // function
console.log(typeof Function.prototype.bind())  // funtion
console.log(Function.prototype.bind.name)  // bind
console.log(Function.prototype.bind().name)  // bound

var obj1 = {
    age: 18
};
function original(a, b){
    console.log(this.age);
    console.log([a, b]);
    return false;
};
var bound = original.bind(obj1, 1);
var boundResult = bound(2); // 18, [1,2]
console.log(boundResult); // fasle
console.log(original.bind.name); // bind
console.log(original.bind.length); // 1
console.log(original.bind().length); // 2  返回 original 函数的形参个数
console.log(bound.name); // bound original
console.log((function(){}).bind().name); // 'bound '
console.log((function(){}).bind().length); // 0
```

**以上可以得出结论：**
1. bind 是 Function 原型链中 Function.prototype 的一个属性值，所有的函数均可调用他。
2. bind 是 一个函数名为 bind 的函数，返回值也是函数，函数名为 'bound '。
3. 调用 bind 的函数中的 this 指向 bind() 函数的第一个参数。
4. 传递给 bind() 的其余参数接收处理了，bind() 后返回的 bound() 函数的参数也接收处理了。将这两次的参数合并处理了。
5. bind() 后的 name 为 'bound 调用bind的函数名'。若是匿名函数则是 'bound '。
6. bind 后的返回值函数，执行后返回值是原函数(original)的返回值。
7. bind 函数的形参是 1，bind 后返回的 bound 函数形参不定，根据绑定的函数原函数(original)形参个数而定。


**根据结论可以模拟实现一个简版bindFn**
```js
Function.prototype.bindFn = function (thisArg){
    if(typeof this !== 'function'){
        throw new TypeError(this + 'must be a function');
    }
    var self = this;
    // 去除 thisArg 的其余参数，转成数组
    var args = [].slice.call(arguments, 1);
    var bound = function(){
        // bound 函数接收的参数转成数组
        var boundArgs = [].slice.call(arguments);
        // apply 修改 this 指向，把两个函数的参数合并传给self函数，并执行，返回执行结果
        return self.apply(thisArg, args.concat(boundArgs));
    }
    return bound;
};

// 测试
var obj = {
    age: 18
};
function original(a,b){
    console.log(this.age);
    console.log([a,b]);
};
var bound = original.bindFn(obj, 1);
bound(2) // 18, [1,2]
```

但是我们知道函数是可以用 new 来实例化的。
```js
var obj = {
    age: 18
};
function original(a,b){
    console.log('this', this);
    console.log('typeof this', typeof this);
    this.age = b;
    console.log('age', this.age);
    console.log('this', this);
    console.log([a, b]);
    return this
};
var bound = original.bind(obj, 1);
var boundResult = bound(2);
console.log(boundResult, 'boundResult'); // {age: 2}
var newBoundResult = new bound(2);
console.log(newBoundResult, 'newBoundResult'); // original {age: 2}
console.log(newBoundResult.constructor); // f original()...
```
可得出以下结论：
1. bind原先指向 obj 失效， 其余参数有效。
2. new bound 的返回值是以 original 原构造函数器生成的新对象，original 原函数的this指向的就是这个新对象。

new 调用时，bind 的返回值函数 bound 内部要模拟实现new 实现的操作
```js
Function.prototype.bindFn = function (thisArg){
    if(typeof this !== 'function'){
        throw new TypeError(this + 'must be a function');
    }
    var self = this;
    // 去除 thisArg 的其余参数，转成数组
    var args = [].slice.call(arguments, 1);
    var Empty = function(){};
    var bound = function(){
        // bound 函数接收的参数转成数组
        var boundArgs = [].slice.call(arguments);
        var finalArgs = args.concat(boundArgs);
        // apply 修改 this 指向，把两个函数的参数合并传给self函数，并执行，返回执行结果
        /**
         * this instanceof Empty ? this  // this 就是new出的obj
         * : thisArgs||this // 如果传递的thisArgs 无效，就将bound的调用者作为this
        **/ 
        return self.apply(this instanceof Empty ? this : thisArgs, finalArgs)
    }
    // 将目标函数的原型对象拷贝到新函数中，因为目标函数可能会被当作构造函数
    Empty.prototype = this.prototype;
    bound.prototype = new Empty();
    
    return bound;
};
```

**最终版本**
```js
Function.prototype.bind = Function.prototype.bind || function(thisArgs){
    if(typeof this !== 'function'){
        throw new TypeError(this + 'nust be a function');
    }
    var self = this;
    var args = [].slice.call(arguments, 1);
    var Empty = function(){};
    var bound = function(){
        var boundArgs = [].slice.call(arguments);
        var finalArgs = args.concat(boundArgs);
        return self.apply(this instanceof Empty ? this: thisArgs, finalArgs);
    }
    Empty.prototype = this.prototype;
    bound.prototype = new Empty();

    return bound;
}
```
