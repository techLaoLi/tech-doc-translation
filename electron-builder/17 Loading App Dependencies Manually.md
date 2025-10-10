# 手动加载应用程序依赖项

警告

重要：此方法只能在开发环境中使用。
由于应用程序的发布版本，应用程序目录应自包含所有使用的文件。

如果在开发环境中，您的应用程序在 /app 文件夹之外运行主进程（由 electron 执行）。您可能需要手动加载 /app 依赖项。因为应用程序依赖项被放置在 /app/node_modules 中，而在不同目录中运行的主进程默认无法访问该目录。

无需在开发 package.json 中复制应用程序依赖项，可以使用如下方法手动使 electron 主进程加载应用程序依赖项：

```javascript
// 假设此文件是：/src/browser/main.js

const path = require('path')
const devMode = (process.argv || []).indexOf('--dev') !== -1

// 在开发模式下加载应用程序依赖项
if (devMode) {
  const PATH_APP_NODE_MODULES = path.join(__dirname, '..', '..', 'app', 'node_modules')
  const Module = require('module')

  // 用于 electron 16 或更低版本
  Module.globalPaths.push(PATH_APP_NODE_MODULES)

  // 用于 electron 17 或更高版本
  const nodeModulePaths = Module._nodeModulePaths
  Module._nodeModulePaths = (from) =>
    nodeModulePaths(from).concat([PATH_APP_NODE_MODULES])
}
```