#### Webpack import 动态加载原理
对于动态导入代码，`Webpack` 会将其转换成自定义的 `__webpack_require__.e` 函数。这个函数返回了一个`Promise`数组，最终模拟出动态导入的效果
```js
__webpack_require__.e = function requireEnsure(chunkId) {
    var promises = [];
    //JSONP 加载模块
    var installedChunkData = installedChunks[chunkId];
    //  判断当前模块是否已加载 0为已加载
    if(installedChunkData !== 0) { 
        // installedChunkData 存在(为一个promise)当前模块是否在加载中，若在加载中，则直接将其push到数组promsie中
        if(installedChunkData) {
            promises.push(installedChunkData[2]);
        } else {
            // 如果模块还未加载，则构造对应的Promise 并缓存在 installedChunks 对象中
            var promise = new Promise(function(resolve, reject) {
                installedChunkData = installedChunks[chunkId] = [resolve, reject];
            });
            promises.push(installedChunkData[2] = promise);

            // 开始模块加载， 构造 script 标签
            var script = document.createElement('script');
            var onScriptComplete;

            script.charset = 'utf-8';
            script.timeout = 120;
            if (__webpack_require__.nc) {
                script.setAttribute("nonce", __webpack_require__.nc);
            }
            script.src = jsonpScriptSrc(chunkId);

            onScriptComplete = function (event) {
                // avoid mem leaks in IE.
                script.onerror = script.onload = null;
                clearTimeout(timeout);
                var chunk = installedChunks[chunkId];
                // 如果 chunk !== 0 则表示加载失败
                if(chunk !== 0) {
                    // 返回错误信息
                    if(chunk) {
                        var errorType = event && (event.type === 'load' ? 'missing' : event.type);
                        var realSrc = event && event.target && event.target.src;
                        var error = new Error('Loading chunk ' + chunkId + ' failed.\n(' + errorType + ': ' + realSrc + ')');
                        error.type = errorType;
                        error.request = realSrc;
                        chunk[1](error);
                    }
                    // 将此模块的记载状态重置为 未加载状态
                    installedChunks[chunkId] = undefined;
                }
            };
            var timeout = setTimeout(function(){
                onScriptComplete({ type: 'timeout', target: script });
            }, 120000);
            // 定义script 标签的 onload 和 onerror 回调
            script.onerror = script.onload = onScriptComplete;
            document.head.appendChild(script);
        }
    }
    return Promise.all(promises);
};
```
__webpack_require__.e主要实现了以下功能：
- 定义一个名为 promises的数组，最终以 Promise.all(promises)形式返回
- 通过 installedChunkData 变量判断当前模块是否已经被加载。如果模块正在加载中，将模块内容 push 到数组 promises 中。如果当前模块没有被加载，则先定义一个对应的 Promise,然后创建一个script 标签，加载模块内容，并定义这个 script标签的 onerror 和 onload 回调
- 最终将新增的script标签对应的 promise(resolve/reject)处理方法定义在webpackJsonpCallback函数中

```js
function webpackJsonpCallback(data) {
    var chunkIds = data[0];
    var moreModules = data[1];

    // 添加"moreModules"到modules对象;然后将所有的chunkIds标记为已加载并触发回调
    var moduleId, chunkId, i = 0, resolves = [];
    for(;i < chunkIds.length; i++) {
        chunkId = chunkIds[i];
        if(installedChunks[chunkId]) {
            resolves.push(installedChunks[chunkId][0]);
        }
        installedChunks[chunkId] = 0;
    }
    for(moduleId in moreModules) {
        if(Object.prototype.hasOwnProperty.call(moreModules, moduleId)) {
            modules[moduleId] = moreModules[moduleId];
        }
    }
    if(parentJsonpFunction) parentJsonpFunction(data);

    while(resolves.length) {
        resolves.shift()();
    }

};
```