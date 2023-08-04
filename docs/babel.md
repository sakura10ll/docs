#### 什么是Babel?
`Babel` 是 `JavaScript` 的 “编译器”。编译是`Babel`的核心目标，因此它自身的实现基于编译原理，深入`AST`来生成目标代码。<br/>主要职责有
- 语法转换，一般是高级语言特性的降级,将es6语法转换为es5语法(`Babel`默认只转换新的语法，而不转换新的`API`)
- `polyfill` 特性的实现和接入 （转换新的API，一类是`Promise`、`Map`、`Symbol`、`Proxy`、`Iterator`等全局对象及对象自身的方法，如 `Object.assign`, `Promise.resolve`；另一类是新的实例方法，如数组实例方法`find`）
- 源码转换，比如 `JSX` 等

------

#### Babel的工具
`@babel/core` 是`babel`实现转换的核心，可以根据配置进行源码的编译和转换<br/>
`@babel/cli` 是`babel`提供的命令行，可以在终端中通过命令行方式运行，编译文件或目录。其负责获取配置内容，依赖 `@babel/core` 完成编译。<br/>
`@babel/preset-env` 是`babel`的预设，根据目标环境按需引入业务所需的`polyfill`<br/>
`@babel/polyfill` 实现es6语法转换为es5语法<br/>
`@babel/pugin-transform-runtime` 的作用是自动移除语法转换后内联的辅助函数，而是使用`@babel/runtime/helpers`里的辅助函数来替代(重复使用`babel`注入的`helper`函数)，达到节省代码空间的目的；对转换的API进行重命名，避免污染全局作用域。<br/>
`@babel/runtime` 包含`babel`编译所需的一些`运行时`helper函数，同时提供了 `regenerator-runtime`包，对`generator`和`async`函数进行编译降级。<br/>

- 关于 `@babel/pugin-transform-runtime` 和 `@babel/runtime`，总结如下：
    - `@babel/pugin-transform-runtime` 需要和 `@babel/runtime` 配合使用。
    - `@babel/pugin-transform-runtime` 在编译时使用，作为 `devDependencies`。
    - `@babel/pugin-transform-runtime` 将业务代码进行编译，引用 `@babel/runtime` 提供的helper函数，达到缩减编译产出代码体积的目的，还能避免污染全局作用域。
    - `@babel/runtime` 用于运行时，作为 `dependencies`。

------

#### Babel的原理
`Babel` 的转码过程重要由三个阶段组成：解析(parse)、转换(transform)、生成(generate)。这三个阶段分别由 `@babel/parser`、`@babel/core`、`@babel/generator` 来完成。
`@babel/parser` 是`babel`用来对 `JavaScript` 语言进行解析的解析器。

- 解析阶段
   该阶段由 `babel` 读取源码并生成 `AST`(抽象语法树)，由两部分组成：词法分析和语法分析。
    - 词法分析：将代码(字符串)分割为`token`流,即语法单元成的数组。
    - 语法分析：分析`token`流(上面生成的数组)并生成 `AST`

- 转换阶段
   根据当前的抽象语法树，以我们定义的规则转换生成新的抽象语法树。通过 `@babel/traverse` 进行遍历，并通过 `@babel/types` 对具体的 AST 节点进行修改。

- 生成阶段
   根据新生成的 `AST` 来生成新的`JS`代码。

