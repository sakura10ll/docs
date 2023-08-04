#### npm 的安装机制
1. 发出 `npm install` 命令时，首先检查 `config`， 获取 `npm` 配置，这里的优先级为：项目的 `.npmrc`文件 > 用户级的 `.npmrc` 文件 > 全局的 `.npmrc` 文件 > `npm`内置的`.npmrc`文件。
2. 然后检查是否存在 `package-lock.json` 文件
- 如果存在，则检查 `package-lock.json` 文件和 `packag.json` 文件内声明的版本是否一致。
   - 若一致，则使用 `package-lock.json` 中的信息，从缓存或网络资源重加载依赖
   - 不一致，则根据 `npm` 版本进行处理（不同版本的`npm`处理方式不一致，
        - `npm` `v5.0.x`: 根据`package-lock.json` 处理；
        - `npm` `v5.1.0-v5.4.2`: 当 `package.json` 声明的 依赖版本规范有符合的更新版本时，忽略`package-lock.json`，按照 `package.json` 安装，并更新 `package-lock.json`
        - `npm` `v5.4.2` 以上: 当`package.json`声明的依赖版本规范与 `package-lock.json` 安装版本兼容时，则根据 `package-lock.json` 安装；如果不兼容，则按照`package.json` 安装，并更新 `package-lock.json`）
- 若不存在，则根据 `package.json` 文件递归构建依赖树，然后按照构建好的依赖树下载完整的依赖资源，在下载时会检查是否存在相关缓存。(缓存目录 `.npm` )
    - 若存在，则将缓存内容解压到 `node_modules` 中
    - 若不存在， 则先从 `npm` 远程仓库下载包资源，检查包的完整性，并将其添加到缓存，同时解压到 `node_modules` 中
3. 最后生成 `package-lock.json` 文件

------

#### npx的作用
1. 可以直接运行 `node_modules/.bin` 文件下的文件。非全局安装`npm`包的情况下，在运行命令时，`npx` 可以自动去 `node_modules/.bin` 路径和环境变量 `$PATH` 里面检查命令是否存在，而不再需要在 `package.json` 中定义相关的`s`。
2. 在执行模块时，会优先安装依赖，但在安装成功后便删除此依赖，避免全局安装带来的问题。