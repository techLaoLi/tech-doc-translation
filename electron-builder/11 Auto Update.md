# 自动更新

自动更新由 electron-updater 包启用。理想情况下，自动更新配置为在 CI 管道中运行以自动配置新版本。有关如何为自动化部署配置本地或 CI 环境的信息，请参见发布配置。

自动更新的工作方式如下：

- 您配置包以构建发布元数据（latest.yml）
- Electron builder 将实际的目标发布和元数据文件上传到配置的目标（通用服务器除外，您必须手动上传）
- 您配置 Electron 应用程序以使用自动更新，它会查询发布服务器以获取可能的新版本

阅读本指南的其余部分以进行配置。

macOS 上需要代码签名

macOS 应用程序必须签名才能使自动更新工作。

## 可自动更新的目标

- macOS: DMG。
- Linux: AppImage, DEB, Pacman (beta) 和 RPM。
- Windows: NSIS。

所有这些目标都是默认的，不需要自定义配置。（尽管可以传递附加配置，例如请求头。）

信息

1. 不支持 Squirrel.Windows。如果使用默认的 NSIS 目标，则在 Windows 上支持简化的自动更新，但 Squirrel.Windows 不支持。
   您可以轻松迁移到 NSIS。
2. macOS 的 zip 目标是 Squirrel.Mac 所必需的，否则无法创建 latest-mac.yml，这会导致 autoUpdater 错误。macOS 的默认目标是 dmg+zip，因此无需显式指定目标。

## electron-updater 和内置 autoUpdater 之间的区别

electron-updater 包提供了与 Electron 内置自动更新器不同的功能。以下是区别：

- 支持 Linux（不仅限于 macOS 和 Windows）。
- 代码签名验证不仅在 macOS 上，也在 Windows 上。
- 所有必需的元数据文件和构件都会自动生成和发布。
- 所有平台都支持下载进度和分阶段推出。
- 开箱即支持不同的提供商：（GitHub Releases、Amazon S3、DigitalOcean Spaces、Keygen 和通用 HTTP(s) 服务器）。
- 您只需要 2 行代码即可使其工作。

## 快速设置指南

1. 将 electron-updater 安装为应用程序依赖项。
2. 根据您想要托管发布文件的位置配置发布选项。
3. 构建您的应用程序并检查构建目录是否包含元数据 .yml 文件以及构建的应用程序。对于大多数发布目标，构建步骤也会上传文件，通用服务器选项除外，您必须手动上传构建的版本和元数据。
4. 使用 electron-updater 中的 autoUpdater 而不是 electron：
   CommonJS
   ```javascript
   const { autoUpdater } = require("electron-updater")
   ```
   ESM
   ```javascript
   import { autoUpdater } from "electron-updater"
   ```
   TypeScript
   ```typescript
   import electronUpdater, { type AppUpdater } from 'electron-updater';

   export function getAutoUpdater(): AppUpdater {
     // 使用解构访问 autoUpdater 由于 'electron-updater' 的 CommonJS 模块。
     // 这是 ESM 兼容性问题的解决方法，请参见 https://github.com/electron-userland/electron-builder/issues/7976。
     const { autoUpdater } = electronUpdater;
     return autoUpdater;
   }
   ```
5. 调用 autoUpdater.checkForUpdatesAndNotify()。或者，如果您需要自定义行为，请实现 electron-updater 事件，请参见下面的示例。

注意

不要调用 setFeedURL。electron-builder 会在构建时自动为您创建 app-update.yml 文件到 resources 中（此文件是内部的，您无需了解）。

## 示例

使用系统通知的 TypeScript 示例

```typescript
import { autoUpdater } from "electron-updater"

export default class AppUpdater {
  constructor() {
    const log = require("electron-log")
    log.transports.file.level = "debug"
    autoUpdater.logger = log
    autoUpdater.checkForUpdatesAndNotify()
  }
}
```

- 显示如何使用的完整示例。
- 通过菜单封装的手动更新。

### 直接实例化更新器的自定义选项

如果您想更多地控制更新器配置（例如，出于授权目的的请求头），您可以直接实例化更新器。

```javascript
import { NsisUpdater } from "electron-updater"
// 或 MacUpdater, AppImageUpdater

export default class AppUpdater {
  constructor() {
    const options = {
      requestHeaders: {
        // 在此处包含任何请求头
      },
      provider: 'generic',
      url: 'https://example.com/auto-updates'
    }

    const autoUpdater = new NsisUpdater(options)
    autoUpdater.addAuthHeader(`Bearer ${token}`)
    autoUpdater.checkForUpdatesAndNotify()
  }
}
```

## 调试

您无需监听所有事件来了解哪里出了问题。只需设置 logger。
推荐使用 electron-log（这是一个额外的依赖项，如有需要您可以安装）。

```javascript
autoUpdater.logger = require("electron-log")
autoUpdater.logger.transports.file.level = "info"
```

请注意，为了在不打包应用程序的情况下开发/测试更新的 UI/UX，您需要在项目根目录中有一个名为 dev-app-update.yml 的文件，该文件与 electron-builder 配置中的发布设置匹配（但采用 yaml 格式）。
在最新版本中，您需要强制更新器在"dev"模式下工作：

```javascript
autoUpdater.forceDevUpdateConfig = true
```

注意

如果您在日志中看到以下内容：

```bash
APPIMAGE env is not defined, current application is not an AppImage
```

您需要应用[此解决方法](https://github.com/electron-userland/electron-builder/issues/3167#issuecomment-627696277)，否则更新将无法继续：
```javascript
process.env.APPIMAGE = path.join(__dirname, 'dist', `app_name-${app.getVersion()}.AppImage`)
```

但不推荐这样做，最好测试已安装的应用程序的自动更新（特别是在 Windows 上）。推荐使用 Minio 作为本地服务器来测试更新。

## 兼容性

生成的元数据文件格式会随时间变化，但兼容性保留到版本 1。如果您开始一个新项目，建议将 electronUpdaterCompatibility 设置为当前最新的格式版本（>= 2.16）。

选项 electronUpdaterCompatibility 设置 electron-updater 兼容性语义版本范围。可以按平台指定。

例如 >= 2.16, >=1.0.0。默认为 >=2.15

- 1.0.0 latest-mac.json
- 2.15.0 path
- 2.16.0 files

## 分阶段推出

分阶段推出允许您将最新版本的应用程序分发给部分用户，并且可以随时间增加，类似于 Google Play 等平台上的推出。

分阶段推出通过手动编辑 latest.yml / latest-mac.yml（渠道更新信息文件）来控制。

```yaml
version: 1.1.0
path: TestApp Setup 1.1.0.exe
sha512: Dj51I0q8aPQ3ioaz9LMqGYujAYRbDNblAQbodDRXAMxmY6hsHqEl3F6SvhfJj5oPhcqdX1ldsgEvfMNXGUXBIw==
stagingPercentage: 10
```

更新将推送给 10% 的用户基础。

如果您想撤回分阶段发布，因为它进行得不好，您必须将版本号提高到高于您损坏的发布版本。
因为您的部分用户将在损坏的 1.0.1 版本上，发布新的 1.0.1 版本将导致他们停留在损坏的版本上。

## 生成并上传的附加文件

latest.yml（或 macOS 的 latest-mac.yml，或 Linux 的 latest-linux.yml）将为除 bintray 之外的所有提供商生成并上传（因为不需要，bintray 不使用 latest.yml）。

## 私有 GitHub 更新仓库

您可以通过设置 GH_TOKEN 环境变量（在用户机器上）和 private 选项，在私有仓库中使用 electron-updater 进行更新。
如果设置了 GH_TOKEN，electron-updater 将使用 GitHub API 进行更新，允许私有仓库工作。

警告

私有 GitHub 提供商仅适用于非常特殊的情况——不适用于所有用户。

注意

GitHub API 目前的速率限制为每个用户每小时 5000 个请求。每次更新检查最多使用 3 个请求。

## 事件

autoUpdater 对象发出以下事件：

#### 事件: error

- error Error

更新时发生错误时发出。

#### 事件: checking-for-update

开始检查更新时发出。

#### 事件: update-available

- info UpdateInfo（用于通用和 github 提供商）| VersionInfo（用于 Bintray 提供商）

有可用更新时发出。如果 autoDownload 为 true，则自动下载更新。

#### 事件: update-not-available

没有可用更新时发出。

- info UpdateInfo（用于通用和 github 提供商）| VersionInfo（用于 Bintray 提供商）

#### 事件: download-progress

- progress ProgressInfo
- bytesPerSecond
- percent
- total
- transferred

进度更新时发出。

#### 事件: update-downloaded

- info UpdateInfo — 用于通用和 github 提供商。VersionInfo 用于 Bintray 提供商。

## UpdateInfo

Electron-Builder / electron-updater / UpdateInfo

#### 扩展自

- UpdateDownloadedEvent

#### 属性

##### files

> readonly files: UpdateFileInfo[]

---

##### minimumSystemVersion?

> readonly optional minimumSystemVersion: string

应用程序运行所需的最低系统版本。示例值：macOS 23.1.0, Windows 10.0.22631。
与 os.release() 值相同，这是内核版本。

---

##### path

> readonly path: string

###### 已弃用

---

##### releaseDate

> releaseDate: string

发布日期。

---

##### releaseName?

> optional releaseName: null | string

发布名称。

---

##### releaseNotes?

> optional releaseNotes: null | string | ReleaseNoteInfo[]

发布说明。如果 updater.fullChangelog 设置为 true，则为列表，否则为字符串。

---

##### sha512

> readonly sha512: string

###### 已弃用

---

##### stagingPercentage?

> readonly optional stagingPercentage: number

分阶段推出百分比，0-100。

---

##### version

> readonly version: string

版本。