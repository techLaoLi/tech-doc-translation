# 通用配置

electron-builder 配置可以定义在：

- 项目 package.json 文件中，使用顶层的 build 键：
```json
"build": {
  "appId": "com.example.app"
}
```

- 或通过 --config <path/to/yml-or-json5-or-toml-or-js> 选项。默认为 electron-builder.yml。
```yaml
appId: "com.example.app"
```

支持 json、json5、toml 或 js/ts（导出的配置或生成配置的函数）格式。

提示：
如果要使用 js 文件，请不要将其命名为 electron-builder.js。这将与 electron-builder 包名冲突。

提示：
如果要使用 toml，请安装 `yarn add toml --dev`。

大多数选项接受 null —— 例如，要显式设置 DMG 图标必须是来自操作系统的默认卷标，并且不应用默认规则（即使用应用程序图标作为 DMG 图标），请将 dmg.icon 设置为 null。

## 构件文件名模板

除了文件宏之外，还支持 ${ext} 宏。

## 来自文件的环境变量

当前目录中的环境文件 electron-builder.env（示例）。仅支持 CLI 使用。

## 如何阅读文档

- 可选属性的名称是正常的，必需的属性是粗体。
- 类型在属性名称后指定：Array<String> | String。像这样的联合类型意味着您可以指定字符串（**/*）或字符串数组（["**/*", "!foo.js"]）。

### 通用配置

Electron-Builder / app-builder-lib / CommonConfiguration

配置选项

##### 扩展自

- Configuration

##### 属性

###### apk?

> readonly optional apk: null | LinuxTargetSpecificOptions

---

###### appId?

> readonly optional appId: null | string

应用程序 ID。在 MacOS 上用作 CFBundleIdentifier，在 Windows 上用作应用程序用户模型 ID（仅限 NSIS 目标，不支持 Squirrel.Windows）。强烈建议设置明确的 ID。

###### # 默认值

```json
com.electron.${name}
```

---

###### appImage?

> readonly optional appImage: null | AppImageOptions

AppImage 选项。

---

###### appx?

> readonly optional appx: null | AppXOptions

---

###### buildDependenciesFromSource?

> optional buildDependenciesFromSource: boolean

是否从源代码构建应用程序原生依赖项。

###### # 默认值

```json
false
```

---

###### buildNumber?

> readonly optional buildNumber: null | string

构建号。映射到 Linux 上使用 FPM 构建的 --iteration 标志。
如果未定义，则会回退到 BUILD_NUMBER 或 TRAVIS_BUILD_NUMBER 或 APPVEYOR_BUILD_NUMBER 或 CIRCLE_BUILD_NUM 或 BUILD_BUILDNUMBER 或 CI_PIPELINE_IID 环境变量。

---

###### buildVersion?

> readonly optional buildVersion: null | string

构建版本。在 macOS 上映射到 CFBundleVersion，在 Windows 上映射到 FileVersion 元数据属性。默认为版本号。
如果未定义 buildVersion 且已定义 buildNumber（或其中一个 buildNumber 环境变量），则将其用作构建版本（version.buildNumber）。

---

###### concurrency?

> readonly optional concurrency: null | Concurrency

[实验性] 并发构建的配置。

---

###### copyright?

> readonly optional copyright: null | string

应用程序的人类可读版权行。

###### # 默认值

```json
Copyright © year ${author}
```

---

###### deb?

> readonly optional deb: null | DebOptions

Debian 包选项。

---

###### directories?

> readonly optional directories: null | MetadataDirectories

构建资源目录

---

###### dmg?

> readonly optional dmg: null | DmgOptions

macOS DMG 选项。

---

###### downloadAlternateFFmpeg?

> readonly optional downloadAlternateFFmpeg: boolean

是否从 Electron 的发布资产中下载备用 FFmpeg 库并在签名前替换默认的 FFmpeg 库

---

###### electronFuses?

> readonly optional electronFuses: null | FuseOptionsV1

传递给 @electron/fuses 的选项
参考：https://github.com/electron/fuses

---

###### extraMetadata?

> readonly optional extraMetadata: any

向 package.json 注入属性。

---

###### flatpak?

> readonly optional flatpak: null | FlatpakOptions

Flatpak 选项。

---

###### forceCodeSigning?

> readonly optional forceCodeSigning: boolean

如果应用程序未签名是否失败（以防止代码签名配置不正确时出现未签名的应用程序）。

###### # 默认值

```json
false
```

---

###### freebsd?

> readonly optional freebsd: null | LinuxTargetSpecificOptions

---

###### includePdb?

> readonly optional includePdb: boolean

是否包含 PDB 文件。

###### # 默认值

```json
false
```

---

###### linux?

> readonly optional linux: null | LinuxConfiguration

与如何构建 Linux 目标相关的选项。

---

###### mac?

> readonly optional mac: null | MacConfiguration

与如何构建 macOS 目标相关的选项。

---

###### mas?

> readonly optional mas: null | MasConfiguration

MAS (Mac 应用商店) 选项。

---

###### masDev?

> readonly optional masDev: null | MasConfiguration

MAS (Mac 应用商店) 开发选项（mas-dev 目标）。

---

###### nativeRebuilder?

> readonly optional nativeRebuilder: null | "legacy" | "sequential" | "parallel"

使用旧的 app-builder 二进制文件安装原生依赖项，或在顺序或并行编译模式下使用 @electron/rebuild。

###### # 默认值

```json
sequential
```

---

###### nodeGypRebuild?

> readonly optional nodeGypRebuild: boolean

在开始打包应用程序之前是否执行 node-gyp rebuild。

不要使用 npm（或 .npmrc）来配置 electron 标头。请使用 electron-builder node-gyp-rebuild。

###### # 默认值

```json
false
```

---

###### npmArgs?

> readonly optional npmArgs: null | string | string[]

安装应用程序原生依赖项时使用的附加命令行参数。

---

###### npmRebuild?

> readonly optional npmRebuild: boolean

在开始打包应用程序之前是否重建原生依赖项。

###### # 默认值

```json
true
```

---

###### nsis?

> readonly optional nsis: null | NsisOptions

---

###### nsisWeb?

> readonly optional nsisWeb: null | NsisWebOptions

---

###### p5p?

> readonly optional p5p: null | LinuxTargetSpecificOptions

---

###### pacman?

> readonly optional pacman: null | LinuxTargetSpecificOptions

---

###### pkg?

> readonly optional pkg: null | PkgOptions

macOS PKG 选项。

---

###### portable?

> readonly optional portable: null | PortableOptions

---

###### productName?

> readonly optional productName: null | string

与 name 类似，但允许您为可执行文件指定包含空格和其他在 name 属性中不允许的特殊字符的产品名称。
如果未在构建配置中指定，则使用在 package.json 顶层定义的 productName 属性。如果在 package.json 顶层未指定，则使用 name 属性。

---

###### removePackageKeywords?

> readonly optional removePackageKeywords: boolean

是否从 package.json 文件中删除 keywords 字段。

###### # 默认值

```json
true
```

---

###### removePackageScripts?

> readonly optional removePackageScripts: boolean

是否从 package.json 文件中删除 scripts 字段。

###### # 默认值

```json
true
```

---

###### rpm?

> readonly optional rpm: null | LinuxTargetSpecificOptions

---

###### snap?

> readonly optional snap: null | SnapOptions

Snap 选项。

---

###### squirrelWindows?

> readonly optional squirrelWindows: null | SquirrelWindowsOptions

---

###### win?

> readonly optional win: null | WindowsConfiguration

与如何构建 Windows 目标相关的选项。

---

## 每个平台可覆盖的选项

如果需要，以下选项也可以在每个平台（顶层键 mac、linux 和 win）中设置。

## 基础配置

Electron-Builder / app-builder-lib / PlatformSpecificBuildOptions

#### 扩展自

- TargetSpecificOptions.FilesBuildOptions

#### 扩展到

- Configuration
- LinuxConfiguration
- MacConfiguration
- WindowsConfiguration

#### 属性

##### appId?

> readonly optional appId: null | string

应用程序 ID。在 MacOS 上用作 CFBundleIdentifier，在 Windows 上用作应用程序用户模型 ID（仅限 NSIS 目标，不支持 Squirrel.Windows）。强烈建议设置明确的 ID。

###### 默认值

```json
com.electron.${name}
```

---

##### artifactName?

> readonly optional artifactName: null | string

构件文件名模板。默认为 ${productName}-${version}.${ext}（某些目标可能有不同的默认值，请参见相应选项）。

###### 覆盖

TargetSpecificOptions.artifactName

---

##### asar?

> readonly optional asar: null | boolean | AsarOptions

是否使用 Electron 的存档格式将应用程序的源代码打包到存档中。

将自动检测必须解包的 Node 模块，您无需显式设置 asarUnpack - 如果不起作用，请提交问题。

###### 默认值

```json
true
```

---

##### asarUnpack?

> readonly optional asarUnpack: null | string | string[]

相对于应用程序目录的 glob 模式，指定创建 asar 存档时要解包的文件。

---

##### compression?

> readonly optional compression: null | CompressionLevel

压缩级别。如果您想快速测试构建，store 可以显著减少构建时间。maximum 不会导致明显的大小差异，但会增加构建时间。

###### 默认值

```json
normal
```

---

##### cscKeyPassword?

> optional cscKeyPassword: null | string

---

##### cscLink?

> optional cscLink: null | string

---

##### defaultArch?

> readonly optional defaultArch: string

---

##### detectUpdateChannel?

> readonly optional detectUpdateChannel: boolean

是否推断更新通道...