# 构建钩子

## 钩子

Node.js 8

所有示例都假设您使用最新的 Node.js 8.11.x 或更高版本。

Electron-Builder / app-builder-lib / Hooks

#### 扩展自

- Configuration

#### 属性

##### afterAllArtifactBuild?

> readonly optional afterAllArtifactBuild: null | string | Hook<BuildResult, string[]>

构建所有构件后要运行的函数（或文件路径或模块 ID）。

```javascript
(buildResult: BuildResult): Promise<Array<string>> | Array<string>
```

配置方式与 afterPack 相同（见上文）。

myAfterAllArtifactBuild.js

```javascript
exports.default = function () {
  // 您可以返回要发布的附加文件
  return ["/path/to/additional/result/file"]
}
```

---

##### afterExtract?

> readonly optional afterExtract: null | string | Hook<PackContext, void>

在预构建的 Electron 二进制文件提取到输出目录后要运行的函数（或文件路径或模块 ID）
设置方式与 beforePack 相同

---

##### afterPack?

> readonly optional afterPack: null | string | Hook<PackContext, void>

在打包后（但在打包为可分发格式和签名之前）要运行的函数（或文件路径或模块 ID）。
设置方式与 beforePack 相同

---

##### afterSign?

> readonly optional afterSign: null | string | Hook<PackContext, void>

在打包和签名后（但在打包为可分发格式之前）要运行的函数（或文件路径或模块 ID）。
设置方式与 beforePack 相同

---

##### appxManifestCreated?

> readonly optional appxManifestCreated: null | string | Hook<string, void>

在磁盘上创建 Appx 清单后但在打包到 .appx 包之前要运行的函数（或文件路径或模块 ID）。

---

##### artifactBuildCompleted?

> readonly optional artifactBuildCompleted: null | string | Hook<ArtifactCreated, void>

在构件构建完成时要运行的函数（或文件路径或模块 ID）。
设置方式与 beforePack 相同

---

##### artifactBuildStarted?

> readonly optional artifactBuildStarted: null | string | Hook<ArtifactBuildStarted, void>

在构件构建开始时要运行的函数（或文件路径或模块 ID）。
设置方式与 beforePack 相同

---

##### beforeBuild?

> readonly optional beforeBuild: null | string | Hook<BeforeBuildContext, boolean | void>

在安装或重建依赖项之前要运行的函数（或文件路径或模块 ID）。当 npmRebuild 设置为 true 时起作用。解析为 false 将跳过依赖项安装或重建。

如果提供了且缺少 node_modules，则不会调用生产依赖项检查。

---

##### beforePack?

> readonly optional beforePack: null | string | Hook<PackContext, void>

打包前要运行的函数（或文件路径或模块 ID）。

```javascript
(context: BeforePackContext): Promise<any> | any
```

作为函数

```javascript
beforePack: async (context) => {
  // 您的代码
}
```

因为在配置文件中无法使用 JavaScript，可以指定为文件路径或模块 ID。函数必须作为默认导出。

```json
"build": {
  "beforePack": "./myBeforePackHook.js"
}
```

项目根目录中的文件 myBeforePackHook.js：

myBeforePackHook.js

```javascript
exports.default = async function(context) {
  // 您的自定义代码
}
```

---

##### electronDist?

> readonly optional electronDist: null | string | Hook<PrepareApplicationStageDirectoryOptions, string>

在暂存 electron 构件环境时要运行的函数（或文件路径或模块 ID）。
返回自定义 Electron 构建的路径（例如 ~/electron/out/R）或 electron zip 文件的文件夹。

Zip 文件必须遵循模式 electron-v${version}-${platformName}-${arch}.zip，否则将假定为未打包的 Electron 应用程序目录

---

##### msiProjectCreated?

> readonly optional msiProjectCreated: null | string | Hook<string, void>

在磁盘上创建 MSI 项目后但在打包到 .msi 包之前要运行的函数（或文件路径或模块 ID）。

---

##### onNodeModuleFile?

> readonly optional onNodeModuleFile: null | string | Hook<string, boolean | void>

在每个 node 模块文件上运行的函数（或文件路径或模块 ID）。返回 true/false 将决定是强制包含还是使用默认复制逻辑