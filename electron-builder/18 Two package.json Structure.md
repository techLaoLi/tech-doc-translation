# 两个 package.json 结构

信息

由于版本 8，electron-builder 仅重建生产依赖项，因此您不必强制使用两个 package.json 结构。

1. 用于开发（./package.json）
   package.json 位于项目根目录。在这里您声明开发环境和构建脚本的依赖项（devDependencies）。
2. 用于您的应用程序（./app/package.json）
   package.json 位于 app 目录中。在此处声明您的应用程序依赖项（dependencies）。只有此目录与最终打包的应用程序一起分发。

为什么？

1. 本地 npm 模块（用 C 编写，而不是 JavaScript）需要编译，这里我们有两个不同的编译目标。那些在应用程序中使用的需要针对 electron 运行时编译，而所有 devDependencies 需要针对您的本地 node.js 环境编译。感谢两个 package.json 结构，这变得微不足道（请参见 #39）。
2. 无需指定要包含在应用程序中的文件（因为开发文件位于应用程序目录之外）。

请参见手动加载应用程序依赖项和 #379。

如果您使用两个 package.json 项目结构，您只会在开发 package.json 中有 devDependencies，而在应用程序 package.json 中有 dependencies。为确保您的依赖项始终根据两个文件进行更新，只需在开发 package.json 中添加 "postinstall": "electron-builder install-app-deps"。这将基本上自动触发应用程序目录中的 npm install，因此您无需在每次安装/更新依赖项时都执行此工作。

元数据

所有元字段应位于 app/package.json 中（版本、名称等）。