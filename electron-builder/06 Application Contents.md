# 应用程序内容

## 文件内容

Electron-Builder / app-builder-lib / FilesBuildOptions

#### 扩展自

- PlatformSpecificBuildOptions

#### 属性

##### extraFiles?

> optional extraFiles: null | string | FileSet | (string | FileSet)[]

与 extraResources 相同，但复制到应用程序的内容目录中（MacOS 为 Contents，Linux 和 Windows 为根目录）。

---

##### extraResources?

> optional extraResources: null | string | FileSet | (string | FileSet)[]

相对于项目目录的 glob 模式，指定时，直接将具有匹配名称的文件或目录复制到应用程序的资源目录中（MacOS 为 Contents/Resources，Linux 和 Windows 为 resources）。

文件模式（以及对 from 和 to 字段的支持）与 files 相同。

---

##### files?

> optional files: null | string | FileSet | (string | FileSet)[]

相对于应用程序目录的 glob 模式，指定复制文件以创建包时要包含的文件。

默认为：

```json
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

开发依赖项在任何情况下都不会被复制。您无需显式忽略它。默认情况下不会忽略隐藏文件，但所有应该忽略的文件都会被默认忽略。

如果您的某些模式不是忽略模式（即不以 ! 开头），则默认模式 **/* 不会添加到您的自定义模式中。package.json 和 **/node_modules/**/*（仅生产依赖项会被复制）在任何情况下都会添加到您的自定义模式中。所有默认忽略都会在任何情况下添加——如果您配置了自己的模式，则无需重复。

可以在平台选项中指定（例如在 mac 中）。

您也可以使用 FileSet 对象而不是简单的 glob 模式来指定自定义源目录和目标目录。

```json
[
  {
    "from": "path/to/source",
    "to": "path/to/destination",
    "filter": ["**/*", "!foo/*.js"]
  }
]
```

您也可以在 from 和 to 字段中使用文件宏。from 和 to 可以是文件，您可以使用此功能在打包时重命名文件。

## FileSet 配置

Electron-Builder / app-builder-lib / FileSet

#### 属性

##### filter?

> optional filter: string | string[]

glob 模式。默认为 "**/*"

---

##### from?

> optional from: string

相对于以下路径的源路径，默认为：

- files 的应用程序目录，
- extraResources 和 extraFiles 的项目目录。
如果您不使用两个 package.json 结构且未设置自定义应用程序目录，则应用程序目录等于项目目录。

---

##### to?

> optional to: string

相对于以下路径的目标路径，默认为：

- files 的 asar 存档根目录，
- extraFiles 的应用程序内容目录，
- extraResources 的应用程序资源目录。