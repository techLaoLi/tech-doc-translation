## electron-builder

一个完整的解决方案，用于打包和构建可分发的 Electron 和 Proton Native 应用程序，支持 macOS、Windows 和 Linux 平台，并内置“自动更新”功能。📦

社区始终在寻找贡献者！👀 开发环境设置简单易行 🪩

### 赞助商

| Notes, Tasks, Projects.All in a Single Place. |
| --- |
| 您了解糖尿病数据的入口 | 一个开源可用的软件许可和分发 API |
| ToDesktop: 构建和发布 Electron 应用的一体化平台 | Dashcam: 使用视频崩溃报告捕捉重现任何错误的步骤的 Electron 工具 |

### 文档

请参阅 electron.build 上的完整文档。

- NPM 包管理：
  - 原生应用程序依赖项编译（包括 Yarn 支持）
  - 开发依赖项永远不会被包含，您无需显式忽略它们
  - 支持双 package.json 结构，但即使您有原生生产依赖项也不强制使用
- 代码签名支持 CI 服务器或开发机器
- 自动更新就绪的应用程序打包
- 多种目标格式：
  - 所有平台：7z, zip, tar.xz, tar.7z, tar.lz, tar.gz, tar.bz2, dir（未打包目录）
  - macOS：dmg, pkg, mas
  - Linux：AppImage, snap, debian 包（deb）, rpm, freebsd, pacman, p5p, apk
  - Windows：nsis（安装程序）, nsis-web（网络安装程序）, portable（免安装便携应用）, AppX（Windows 商店）, MSI, Squirrel.Windows
- 将构件发布到 GitHub Releases、Amazon S3、DigitalOcean Spaces 和 Bintray
- 高级构建功能：
  - 将已打包的应用程序打包为可分发格式
  - 独立构建步骤
  - 并行构建和发布，在 CI 服务器上使用硬链接减少 IO 和磁盘空间使用
  - electron-compile 支持（在构建时即时编译）
- Docker 镜像可在任何平台上为 Linux 或 Windows 构建 Electron 应用
- Proton Native 支持
- 按需自动下载所有必需的工具文件（例如代码签名 Windows 应用程序、制作 AppX），无需设置

| 问题 | 答案 |
| --- | --- |
| "我想配置 electron-builder" | 查看选项 |
| "我发现了一个 bug 或我有问题" | 提交 issue |
| "我想支持开发" | 捐赠 |

### 安装

强烈推荐使用 Yarn 而不是 npm。

```
yarn add electron-builder --dev
```

#### Yarn 3 注意事项

Yarn 3 默认使用 PnP，但 electron-builder 仍需要 node-modules（参考：yarnpkg/berry#4804）。在 .yarnrc.yaml 中添加如下配置：

```
nodeLinker: "node-modules"
```

将声明使用 node-modules 而不是 PnP。

### 快速设置指南

electron-webpack-quick-start 是创建新 Electron 应用程序的推荐方式。请参阅样板。

1. 在应用程序 package.json 中指定标准字段——name、description、version 和 author。
2. 在 package.json 中按如下方式指定构建配置：

```json
"build": {
  "appId": "your.id",
  "mac": {
    "category": "your.app.category.type"
  }
}
```

查看所有选项。Option files 用于指示哪些文件应该打包到最终应用程序中，包括入口文件（可能必需）。
您也可以使用独立的配置文件，如 js、ts、yml 和 json/json5。有关支持的扩展名，请参阅 read-config-file。程序化 API 的 JS 示例

3. 添加图标。
4. 在开发 package.json 中添加 scripts 键：

```json
"scripts": {
  "app:dir": "electron-builder --dir",
  "app:dist": "electron-builder"
}
```

然后您可以运行 `yarn app:dist`（以可分发格式打包，例如 dmg、windows 安装程序、deb 包）或 `yarn app:dir`（仅生成包目录而不真正打包，这对测试目的很有用）。
为了确保您的原生依赖项始终与 Electron 版本匹配，只需在 package.json 中添加脚本 `"postinstall": "electron-builder install-app-deps"`。
5. 如果您有自己的作为应用程序一部分的原生插件（而不是作为依赖项），请将 nodeGypRebuild 设置为 true。

请注意，默认情况下所有内容都打包到 asar 存档中。

对于要发布到生产环境的应用程序，您应该签署您的应用程序。请参阅何处购买代码签名证书。

### 程序化使用

请参阅 node_modules/electron-builder/out/index.d.ts。提供了 TypeScript 类型定义，也可以在这里找到。

下面提供的代码片段也在此处"实际操作"展示。

```javascript
"use strict"

const builder = require("electron-builder")
const Platform = builder.Platform

// 返回 Promise
builder.build({
  targets: Platform.MAC.createTarget(),
  config: {
    "//": "构建选项，请参见 https://www.electron.build/"
  }
})
.then(() => {
  // 处理结果
})
.catch((error) => {
  // 处理错误
})
```

### 样板

- electron-webpack-quick-start — 开发 electron-webpack 的最小项目结构，推荐使用
- electron-react-boilerplate — 可扩展跨平台桌面应用程序的样板
- electron-react-redux-boilerplate — 使用 Electron、React 和 Redux 的最小样板
- electron-boilerplate — 最小但全面的样板应用程序
- Vue CLI 3 plugin for Electron — 无需配置的 Electron Vue CLI 3 插件
- electron-vue-vite — 简单的 Electron + Vue3 + Vite5 样板
- vite-electron-builder — 基于 Vite 的 Electron 应用安全样板，支持多种框架
- electronjs-with-nextjs — 使用 NextJS 和 TypeScript 的 ElectronJS 应用程序

### 调试

设置 DEBUG 环境变量来调试 electron-builder 正在执行的操作：

```
DEBUG=electron-builder
```

FPM_DEBUG 环境变量用于添加更多关于构建 Linux 目标（除 snap 和 appimage 外）的详细信息。

DEBUG_DMG=true 环境变量用于从 hdiutil (macOS) 添加更多调试/详细信息。

#### cmd

在 Windows 上使用 set 命令设置环境变量：

```
set DEBUG=electron-builder
```

#### PowerShell

PowerShell 使用不同的语法设置环境变量：

```
$env:DEBUG=electron-builder
```

### 捐赠

我们在空闲时间做这些开源工作。如果您希望我们投入更多时间，请捐赠。