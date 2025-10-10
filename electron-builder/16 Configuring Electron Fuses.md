# 配置 Electron Fuses

注意

以下信息部分复制自与 @electron/fuses 的集成和 electron 教程，以便于阅读/访问。

## 什么是 fuses？

对于 Electron 功能的子集，为整个应用程序禁用某些功能是有意义的。例如，99% 的应用程序不使用 ELECTRON_RUN_AS_NODE，这些应用程序希望能够交付一个无法使用该功能的二进制文件。我们也不希望 Electron 用户从源代码构建 Electron，因为这既是一个巨大的技术挑战，也需要大量的时间和金钱成本。

Fuses 是这个问题的解决方案，从高层次上讲，它们是 Electron 二进制文件中的"魔术位"，可以在打包 Electron 应用程序时翻转以启用/禁用某些功能/限制。由于它们在您对应用程序进行代码签名之前在打包时被翻转，操作系统会负责确保这些位不会通过操作系统级别的代码签名验证（Gatekeeper / App Locker）被翻转回来。

## 我如何翻转 fuses？

在底层，electron-builder 利用官方的 @electron/fuses 模块来轻松翻转这些 fuses。以前，electron fuses 只能在 afterPack 钩子中翻转（这仍然是支持的方法）。现在，您可以设置 electron-builder 配置属性 electronFuses: FuseOptionsV1 来激活 electron-builder 的集成。

### 示例

注意

下面的 true/false 只是一个示例，请根据您自己的要求自定义配置

```javascript
electronFuses: {
  runAsNode: false,
  enableCookieEncryption: true,
  enableNodeOptionsEnvironmentVariable: false,
  enableNodeCliInspectArguments: false,
  enableEmbeddedAsarIntegrityValidation: true,
  onlyLoadAppFromAsar: true,
  loadBrowserProcessSpecificV8Snapshot: false,
  grantFileProtocolExtraPrivileges: false
}
```

仍然可以继续在 afterPack 钩子中保持您当前的逻辑流程，因此在 PlatformPackager 中已经公开了一个便捷方法以方便自定义标志。它直接接受 AfterPackContext 和 FuseConfig 对象类型的参数。
这个便捷方法被开放，以便可以提供自定义 FuseConfig，允许使用 strictlyRequireAllFuses 来监控您的 fuses 并在 fuses 发布时保持更新，和/或如果要利用更新，可以覆盖 electron-builder 中 @electron/fuses 的版本。

afterPack.ts

```typescript
const { FuseConfig, FuseVersion, FuseV1Options } = require("@electron/fuses")

exports.default = function (context: AfterPackContext) {
  const fuses: FuseConfig = {
    version: FuseVersion.V1,
    strictlyRequireAllFuses: true,
    [FuseV1Options.RunAsNode]: false,
    ... // 必须指定所有其他标志，因为 `strictlyRequireAllFuses = true`
  }
  await context.packager.addElectronFuses(context, fuses)
}
```

## 验证 Fuses

您可以使用 fuses CLI 验证 fuses 是否已被翻转或检查任意 Electron 应用程序的 fuse 状态。

```bash
npx @electron/fuses read --app /Applications/Foo.app
```

## 类型文档

Electron-Builder / app-builder-lib / FuseOptionsV1

所有选项都来自 @electron/fuses
参考：https://raw.githubusercontent.com/electron/electron/refs/heads/main/docs/tutorial/fuses.md

#### 属性

##### enableCookieEncryption?

> optional enableCookieEncryption: boolean

cookieEncryption fuse 切换磁盘上的 cookie 存储是否使用 OS 级别的加密密钥进行加密。默认情况下，Chromium 用于存储 cookie 的 sqlite 数据库以明文形式存储值。如果您希望确保您的应用程序 cookie 以与 Chrome 相同的方式加密，则应启用此 fuse。请注意，这是一个单向转换，如果您启用此 fuse，现有的未加密 cookie 将在写入时加密，但如果您随后禁用该 fuse，您的 cookie 存储将实际上损坏且无法使用。大多数应用程序可以安全地启用此 fuse。

---

##### enableEmbeddedAsarIntegrityValidation?

> optional enableEmbeddedAsarIntegrityValidation: boolean

embeddedAsarIntegrityValidation fuse 在 macOS 上切换一个实验性功能，该功能在加载时验证 app.asar 文件的内容。此功能旨在对性能影响最小，但可能会略微减慢从 app.asar 存档中读取文件的速度。
目前，ASAR 完整性检查支持：

- macOS 从 electron>=16.0.0 开始
- Windows 从 electron>=30.0.0 开始

有关如何使用 asar 完整性验证的更多信息，请阅读 Asar 完整性文档。

---

##### enableNodeCliInspectArguments?

> optional enableNodeCliInspectArguments: boolean

nodeCliInspect fuse 切换是否尊重 --inspect、--inspect-brk 等标志。禁用时，它还确保 SIGUSR1 信号不会初始化主进程检查器。大多数应用程序可以安全地禁用此 fuse。

---

##### enableNodeOptionsEnvironmentVariable?

> optional enableNodeOptionsEnvironmentVariable: boolean

nodeOptions fuse 切换是否尊重 NODE_OPTIONS 和 NODE_EXTRA_CA_CERTS 环境变量。NODE_OPTIONS 环境变量可用于向 Node.js 运行时传递各种自定义选项，在生产环境中应用程序通常不使用。大多数应用程序可以安全地禁用此 fuse。

---

##### grantFileProtocolExtraPrivileges?

> optional grantFileProtocolExtraPrivileges: boolean

grantFileProtocolExtraPrivileges fuse 改变了从 file:// 协议加载的页面是否被授予超出传统 Web 浏览器中接收的权限。此行为是原始版本 Electron 中应用程序的核心，但现在不再需要，因为应用程序应该从自定义协议提供本地文件。如果您不从 file:// 提供页面，则应禁用此 fuse。
此 fuse 授予 file:// 协议的额外权限不完整地记录如下：

- file:// 协议页面可以使用 fetch 从 file:// 加载其他资产
- file:// 协议页面可以使用 service workers
- file:// 协议页面被授予对也运行在 file:// 协议上的子框架的通用访问权限，无论沙箱设置如何

---

##### loadBrowserProcessSpecificV8Snapshot?

> optional loadBrowserProcessSpecificV8Snapshot: boolean

loadBrowserProcessSpecificV8Snapshot fuse 改变了 Electron 浏览器进程使用哪个 V8 快照文件。默认情况下，Electron 的进程都将使用相同的 V8 快照文件。启用此 fuse 时，浏览器进程使用名为 browser_v8_context_snapshot.bin 的文件作为其 V8 快照。其他进程将继续使用它们通常使用的 V8 快照文件。

---

##### onlyLoadAppFromAsar?

> optional onlyLoadAppFromAsar: boolean

onlyLoadAppFromAsar fuse 改变了 Electron 用于定位您的应用程序代码的搜索系统。默认情况下，Electron 将按以下顺序搜索 app.asar -> app -> default_app.asar。启用此 fuse 时，搜索顺序变为单个条目 app.asar，从而确保当与 embeddedAsarIntegrityValidation fuse 结合使用时，无法加载未经验证的代码。

---

##### resetAdHocDarwinSignature?

> optional resetAdHocDarwinSignature: boolean

重置应用程序签名，专门用于 macOS。
注意：这应该是不必要的，因为 electron-builder 在翻转 fuses 后直接签署应用程序。
参考：https://github.com/electron/fuses?tab=readme-ov-file#apple-silicon

---

##### runAsNode?

> optional runAsNode: boolean

runAsNode fuse 切换是否尊重 ELECTRON_RUN_AS_NODE 环境变量。请注意，如果禁用此 fuse，则主进程中的 process.fork 将无法正常工作，因为它依赖于此环境变量来运行。相反，我们建议您使用实用程序进程，它们适用于许多需要独立 Node.js 进程的用例（如 Sqlite 服务器进程或类似场景）。