#### 什么是 Tree shaking?
Tree Shaking 是一种基于 ES Module 规范的 Dead Code Elimination(死码消除)技术，它会在运行过程中静态分析模块之间的导入导出，确定 ESM 模块中哪些导出值未被其他模块使用，并将其删除，以此实现打包产物的优化。

#### Tree Shaking 为什么要依赖ESM规范？
Tree Shaking 是在编译时进行未引用代码消除的，因此需要在编译时确定依赖关系，进而确定哪些代码可以去除。<br/>
ESM 规范具备以下特点：
- import 模块名只能是字符串常量
- import 一般只能在模块的顶层出现
- import 依赖的内容是不可变的

这些特点使得 ESM 规范具有静态分析能力。而CommonJS定义的模块化规范，只有在执行代码时才能动态确定依赖模块，因此不具备 Tree Shaking 的先天条件。

-----
#### 什么是副作用模块，如何对副作用模块进行 Tree Shaking 操作？
"副作用模块"与 "副作用函数" 类似。<br/>
"副作用函数"的典型例子：
1. 与外界交互的。
```js
let num = 0;
function sum(x,y){
    num = x + y;
    return sum
}
```
2. 调用I/O的
```js
function sum(x,y){
    console.log(x,y)
    return x + y
}
```
3. 从函数范围之外检索值
```js
function of(){
    return this._value
}
```
4. 磁盘检索
```js
function getRandom(){
    return Math.random()
}
```
5. 抛出异常
```js
function sum(x, y){
    throw new Error()
    return x + y
}
```
具有副作用的模块难以被Tree Shaking 优化，为了解决这个问题，webpack提供 sideEffects 配置，在 package.json 中添加该属性来告诉工程化工具，哪些模块具有副作用，哪些模块没有副作用并可以被 Tree Shaking 优化。
webpack 趋向于保留整个默认导出对象或类。只处理函数和顶层的import/expor变量，不能将没用到的对象或类内部的方法消除。
以下情况都不利于 Tree Shaking 处理
- 导出一个包含多个属性和方法的对象
- 导出一个包含多个属性和方法的类
- 使用 expor default 导出
