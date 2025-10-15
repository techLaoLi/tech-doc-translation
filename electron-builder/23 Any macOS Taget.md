# 任何 macOS 目标

顶级的 [mac](https://www.electron.build/configuration/mac) 键包含了一组选项，用于指导 electron-builder 如何构建 macOS 目标。这些选项适用于任何 macOS 目标。

## 配置

Electron-Builder / app-builder-lib / MacConfiguration

#### 继承自

- PlatformSpecificBuildOptions

#### 被以下扩展

- MasConfiguration

#### 属性

##### additionalArguments?

> readonly optional additionalArguments: null | string[]

指定要传递给用于签名特定文件的 codesign 命令的附加参数数组。

您在 Electron 应用中包含的一些子资源可能需要用 --deep 进行签名，但这通常不安全地应用于整个 Electron 应用，因此应该仅应用于您的文件。
使用示例: ['--deep']

---

##### appId?

> readonly optional appId: null | string

应用程序 ID。用作 MacOS 的 CFBundleIdentifier 和 Windows 的 Application User Model ID（仅限 NSIS 目标，不支持 Squirrel.Windows）。强烈建议设置明确的 ID。

###### 默认值

```
com.electron.${name}
```

###### 继承自

PlatformSpecificBuildOptions.appId

---

##### artifactName?

> readonly optional artifactName: null | string

工件文件名模板。默认为 ${productName}-${version}.${ext}（某些目标可以有其他默认值，请参见相应选项）。

###### 继承自

PlatformSpecificBuildOptions.artifactName

---

##### asar?

> readonly optional asar: null | boolean | AsarOptions

是否使用 Electron 的归档格式将应用程序的源代码打包到归档中。

必须解压的 Node 模块将被自动检测，您不需要显式设置 asarUnpack - 如果这不起作用，请提交问题。

###### 默认值

```
true
```

###### 继承自

PlatformSpecificBuildOptions.asar

---

##### asarUnpack?

> readonly optional asarUnpack: null | string | string[]

相对于应用程序目录的 glob 模式，指定在创建 asar 归档时要解包哪些文件。

###### 继承自

PlatformSpecificBuildOptions.asarUnpack

---

##### binaries?

> readonly optional binaries: null | string[]

需要签名的额外二进制文件的路径。

---

##### bundleShortVersion?

> readonly optional bundleShortVersion: null | string

CFBundleShortVersionString。除非您需要，否则不要使用它。

---

##### bundleVersion?

> readonly optional bundleVersion: null | string

CFBundleVersion。除非您需要，否则不要使用它。

---

##### category?

> readonly optional category: null | string

应用程序类别类型，在 Finder 中通过"查看"->"按应用程序类别排列"查看应用程序目录时显示。

例如，"category": "public.app-category.developer-tools" 将应用程序类别设置为开发者工具。

有效值在 Apple 的文档中列出。

---

##### compression?

> readonly optional compression: null | CompressionLevel

压缩级别。如果您想快速测试构建，store 可以显著减少构建时间。maximum 不会导致明显的大小差异，但会增加构建时间。

###### 默认值

```
normal
```

###### 继承自

PlatformSpecificBuildOptions.compression

---

##### cscKeyPassword?

> optional cscKeyPassword: null | string

###### 继承自

PlatformSpecificBuildOptions.cscKeyPassword

---

##### cscLink?

> optional cscLink: null | string

###### 继承自

PlatformSpecificBuildOptions.cscLink

---

##### darkModeSupport?

> readonly optional darkModeSupport: boolean

是否支持深色模式。如果您的应用程序有深色模式，您可以使您的应用程序遵循系统范围的深色模式设置。

###### 默认值

```
false
```

---

##### defaultArch?

> readonly optional defaultArch: string

###### 继承自

PlatformSpecificBuildOptions.defaultArch

---

##### detectUpdateChannel?

> readonly optional detectUpdateChannel: boolean

是否从应用程序版本的预发布组件推断更新频道。例如，如果版本为 0.12.1-alpha.1，则频道将设置为 alpha。否则设置为 latest。
这不适用于 github 发布，github 发布永远不会自动检测更新频道。

###### 默认值

```
true
```

###### 继承自

PlatformSpecificBuildOptions.detectUpdateChannel

---

##### disableDefaultIgnoredFiles?

> optional disableDefaultIgnoredFiles: null | boolean

是否排除所有默认忽略的文件(https://www.electron.build/contents#files)和选项。默认为 false。

###### 默认值

```
false
```

###### 继承自

PlatformSpecificBuildOptions.disableDefaultIgnoredFiles

---

##### electronLanguages?

> readonly optional electronLanguages: string | string[]

要保留的 electron 语言环境。默认情况下，所有 Electron 语言环境都按原样使用。

###### 继承自

PlatformSpecificBuildOptions.electronLanguages

---

##### electronUpdaterCompatibility?

> readonly optional electronUpdaterCompatibility: null | string

electron-updater 兼容性语义版本范围。

###### 继承自

PlatformSpecificBuildOptions.electronUpdaterCompatibility

---

##### entitlements?

> readonly optional entitlements: null | string

用于签名应用程序的权限文件路径。如果存在 build/entitlements.mac.plist 将被使用（这是推荐的设置方式）。
MAS 权限在 mas 中指定。
请参见 osx-sign 存储库中的此文件夹以获取示例。
请注意，如果未设置正确的权限，您的应用程序可能会崩溃，例如在 Electron 20+ 的 arm64 构建上需要 com.apple.security.cs.allow-jit。
请参阅 Electron 文档中的 Signing and Notarizing macOS Builds 了解更多信息。

---

##### entitlementsInherit?

> readonly optional entitlementsInherit: null | string

继承安全设置以签名分发框架和包的子权限路径。如果存在 build/entitlements.mac.inherit.plist 将被使用（这是推荐的设置方式）。
请参见 osx-sign 存储库中的此文件夹以获取示例。

此选项仅在提供权限时适用。

---

##### entitlementsLoginHelper?

> readonly optional entitlementsLoginHelper: null | string

登录助手权限文件路径。
使用 App Sandbox 时，通常在继承权限中的 com.apple.security.inherit 密钥无法继承，因为登录助手是独立的可执行文件。
默认为 entitlements 提供的值。此选项仅在提供权限时适用。

---

##### executableName?

> readonly optional executableName: null | string

可执行文件名。默认为 productName。

###### 继承自

PlatformSpecificBuildOptions.executableName

---

##### extendInfo?

> readonly optional extendInfo: any

Info.plist 的额外条目。

---

##### extraDistFiles?

> readonly optional extraDistFiles: null | string | string[]

要放入归档的额外文件。不适用于 tar.*。

---

##### extraFiles?

> optional extraFiles: null | string | FileSet | (string | FileSet)[]

与 extraResources 相同，但复制到应用程序的内容目录中（MacOS 的 Contents，Linux 和 Windows 的根目录）。

###### 继承自

PlatformSpecificBuildOptions.extraFiles

---

##### extraResources?

> optional extraResources: null | string | FileSet | (string | FileSet)[]

相对于项目目录的 glob 模式，指定时，直接将匹配名称的文件或目录复制到应用程序的资源目录中（MacOS 的 Contents/Resources，Linux 和 Windows 的 resources）。

文件模式（以及对 from 和 to 字段的支持）与 files 相同。

###### 继承自

PlatformSpecificBuildOptions.extraResources

---

##### fileAssociations?

> readonly optional fileAssociations: FileAssociation | FileAssociation[]

文件关联。

###### 继承自

PlatformSpecificBuildOptions.fileAssociations

---

##### files?

> optional files: null | string | FileSet | (string | FileSet)[]

相对于应用程序目录的 glob 模式，指定在复制文件创建包时包含哪些文件。

默认为：

```
[
  "**/*",
  "!**/node_modules/*/{CHANGELOG.md,README.md,README,readme.md,readme}",
  "!**/node_modules/*/{test,__tests__,tests,powered-test,example,examples}",
  "!**/node_modules/*.d.ts",
  "!**/node_modules/.bin",
  "!**/*.{iml,o,hprof,orig,pyc,pyo,rbc,swp,csproj,sln,xproj}",
  "!.editorconfig",
  "!**/._*",
  "!**/{.DS_Store,.git,.hg,.svn,CVS,RCS,SCCS,.gitignore,.gitattributes}",
  "!**/{__pycache__,thumbs.db,.flowconfig,.idea,.vs,.nyc_output}",
  "!**/{appveyor.yml,.travis.yml,circle.yml}",
  "!**/{npm-debug.log,yarn.lock,.yarn-integrity,.yarn-metadata.json}"
]
```

开发依赖项永远不会被复制。您不需要显式忽略它。默认情况下不忽略隐藏文件，但所有应该忽略的文件都会被默认忽略。

如果您的某些模式不是忽略模式（即不以 ! 开头），则默认模式 **/* 不会添加到您的自定义模式中。package.json 和 **/node_modules/**/*（仅生产依赖项将被复制）在任何情况下都会添加到您的自定义模式中。所有默认忽略都会在任何情况下添加——如果您配置了自己的模式，则不需要重复。

可以在平台选项中指定（例如在 mac 中）。

您也可以使用 FileSet 对象而不是简单的 glob 模式来指定自定义源目录和目标目录。

```
[
  {
    "from": "path/to/source",
    "to": "path/to/destination",
    "filter": ["**/*", "!foo/*.js"]
  }
]
```

您可以在 from 和 to 字段中使用文件宏。from 和 to 可以是文件，您可以使用此功能在打包时重命名文件。

###### 继承自

PlatformSpecificBuildOptions.files

---

##### forceCodeSigning?

> readonly optional forceCodeSigning: boolean

是否需要代码签名。如果未设置 CSC_LINK，则默认为 false，否则为 true。

---

##### gatekeeperAssess?

> readonly optional gatekeeperAssess: boolean

是否在打包后运行 gatekeeper-assess。

###### 默认值

```
false
```

---

##### hardenedRuntime?

> readonly optional hardenedRuntime: boolean

是否启用 hardenedRuntime。如果未显式设置，则在 Electron 13.6.0 或更高版本上默认启用。

---

##### icon?

> optional icon: null | string

应用程序图标的路径。默认为构建目录中的应用程序图标，如果存在则使用。如果不存在并且在根目录中存在图标.icns，则将使用它。

###### 继承自

PlatformSpecificBuildOptions.icon

---

##### identity?

> readonly optional identity: null | string

签名时使用的证书名称。建议使用环境变量CSC_LINK或CSC_NAME，而不是指定此选项。MAS安装程序的标识在mas中指定。
设置为 - 使用临时标识进行签名。设置为 null 可完全跳过签名。

---

##### identityValidation?

> readonly optional identityValidation: boolean

是否启用标识验证。默认为 true。

---

##### minimumSystemVersion?

> readonly optional minimumSystemVersion: null | string

应用程序支持的最低 macOS 版本。例如，10.15 或 11.0。

---

##### notarize?

> readonly optional notarize: null | boolean | NotarizeLegacyOptions | NotarizeNotaryOptions

是否对 macOS 应用程序进行公证。

###### 默认值

```
false
```

---

##### provisioningProfile?

> readonly optional provisioningProfile: null | string

用于签名的预配配置文件的路径。

---

##### protocols?

> readonly optional protocols: Protocol | Protocol[]

应用程序支持的协议。

###### 继承自

PlatformSpecificBuildOptions.protocols

---

##### publish?

> optional publish: null | string | string[] | PublishConfiguration | PublishConfiguration[]

发布选项。可以是字符串、字符串数组或发布配置对象或对象数组。如果未设置，则将从环境变量或 CI 中推断发布提供者。

###### 继承自

PlatformSpecificBuildOptions.publish

---

##### releaseInfo?

> optional releaseInfo: ReleaseInfo

发布信息。

###### 继承自

PlatformSpecificBuildOptions.releaseInfo

---

##### target?

> optional target: null | string | TargetConfiguration | (string | TargetConfiguration)[]

目标。可以是字符串、TargetConfiguration 或它们的数组。

###### 继承自

PlatformSpecificBuildOptions.target

---

##### type?

> readonly optional type: null | string

如何打包应用程序。默认为应用程序，这意味着将创建一个简单的应用程序包。

---
