**new 操作符做了什么：**
  1. 建立一个空对象， 这个对象将会作为执行构造函数之后返回的对象实例
  2. 使上面的空对象的原型(__proto__) 指向构造函数的prototype 属性
  3. 将这个空对象赋值给构造函数内部的this，并执行构造函数逻辑
  4. 根据构造函数执行逻辑，返回第一步创建的对象或构造函数的显示返回值（若是函数没有返回对象类型Object(包含Funtion，Array，Date，RegExg，Error)，那么 new 表达式中的函数调用会自动返回这个新的对象）

```js
function newOperator(ctor, ...args){
    if(typeof ctor !== 'function'){
        throw 'newOperator function the first param must be a function';
    }
    // 1. 建立一个全新的对象
    // 2. 执行 [[prototype]] 连接
    // 4. 经过 new 建立的每一个对象将最终被 [[prototype]] 连接到这个函数的 prototype 对象上
    var newObj = Object.create(ctor.prototype); 
    /** 
      * var newObj = Object.create(ctor.prototype);  
      * 相当于 
      * var newObj = {}; 
      * newObj.__proto__ = ctor.prototype;
    **/
    // 3. 生成的新对象会绑定到函数调用的this,即obj绑定到构造函数上，可以访问构造函数中的属性
    var ctorReturnResult = ctor.apply(newObj, args);
    var isObject = typeof ctorReturnResult === 'object' && ctorReturnResult !== null;
    var isFunction = typeof ctorReturnResult === 'function';
    if(isObject || isFunction){
        return ctorReturnResult;
    }
    return newObj;
}


// 测试
function Student(name, age){
    this.name = name;
    this.age = age;
}
Student.prototype.doSth = function(){
    console.log(this.name);
};
var student1 = newOperator(Student,"张三", 18);
var student2 = newOperator(Student,"李四", 18);
student1.doSth(); // 张三
student2.doSth(); // 李四
student1.__proto__ === Student.prototype; // true
student2.__proto__ === Student.prototype; // true
```


Object.create(arg, [pro]) 创建对象的原型取决于arg, arg为null，新对象是空对象，没有原型，不继承任何对象；arg 为指定对象，新对象的原型指向指定对象，继承指定对象。

实现Object.create()
```js
function objectCreate(pro){
    function F(){};
    F.prototype = proto;
    return new F();
}
```