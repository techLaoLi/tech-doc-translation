# 命令行界面 (CLI)

```
命令:
electron-builder build                    构建                      [默认]
electron-builder install-app-deps         安装应用依赖
electron-builder node-gyp-rebuild         重新构建自己的原生代码
electron-builder create-self-signed-cert  为 Windows 应用创建自签名代码签名证书
electron-builder start                    使用 electron-webpack 在开发模式下运行应用
electron-builder publish                  使用您的发布配置发布任何文件

构建:
--mac, -m, -o, --macos   为 macOS 构建，接受目标列表 (参见
https://www.electron.build/mac)               [数组]
--linux, -l              为 Linux 构建，接受目标列表 (参见
https://www.electron.build/linux)             [数组]
--win, -w, --windows     为 Windows 构建，接受目标列表 (参见
https://www.electron.build/win)               [数组]
--x64                    为 x64 构建                               [布尔值]
--ia32                   为 ia32 构建                              [布尔值]
--armv7l                 为 armv7l 构建                            [布尔值]
--arm64                  为 arm64 构建                             [布尔值]
--universal              为通用架构构建 (仅限 Mac)                  [布尔值]
--dir                    构建未打包目录。适用于测试。              [布尔值]
--prepackaged, --pd      预打包应用的路径 (打包为可分发格式)
--projectDir, --project  项目目录的路径。默认为当前工作目录。
--config, -c             electron-builder 配置的路径。默认为
`electron-builder.yml` (或 `json`, 或 `json5`, 或 `js`, 或 `ts`)，参见
https://goo.gl/YFRJOM

发布:
--publish, -p  发布构件 (到 GitHub Releases)，参见
https://www.electron.build/publish
[选择: "onTag", "onTagOrDraft", "always", "never", undefined]


-----

electron-builder publish

发布构件列表

选项:
-v, --version  搜索上传版本时使用的应用/构建版本
(某些发布器使用)                              [字符串]
--help     显示帮助                                             [布尔值]
-f, --files    上传到发布器的文件                          [数组] [必需]
-c, --config   electron-builder 配置的路径。默认为
`electron-builder.yml` (或 `json`, 或 `json5`, 或 `js`, 或
`ts`)，参见 https://www.electron.build/configuration   [字符串]

其他:
--help     显示帮助                                                 [布尔值]
--version  显示版本号                                                [布尔值]
```

对于其他命令，请使用 --help 参数查看帮助，例如 ./node_modules/.bin/electron-builder install-app-deps --help

提示

由于 Node.js 8 内置了 npx，因此您可以简单地使用 npx electron-builder。

如果从终端而非 package.json 脚本运行示例命令，请在前面加上 npx。

为 macOS、Windows 和 Linux 构建

electron-builder -mwl

为 Linux 构建 deb 和 tar.xz

electron-builder --linux deb tar.xz

为 Windows 构建 NSIS 32位安装程序

electron-builder --windows nsis:ia32

设置 package.json 属性 foo 为 bar

electron-builder -c.extraMetadata.foo=bar

为 NSIS 配置 Unicode 选项

electron-builder -c.nsis.unicode=false

## 目标

如果没有目标配置，electron-builder 将使用默认目标为当前平台和当前架构构建 Electron 应用。

- macOS - 用于 Squirrel.Mac 的 DMG 和 ZIP。
- Windows - [NSIS](./nsis.md)。
- Linux:
  - 如果在 Windows 或 macOS 上构建：x64 的 Snap 和 AppImage。
  - 如果在 Linux 上构建：当前架构的 Snap 和 AppImage。

平台和架构可以通过 CLI 参数或在配置中进行配置。

例如，如果您不想每次都传递 --ia32 和 --x64 标志，而是默认为 Windows 构建所有架构的 NSIS 目标：

### 配置

package.json

```json
"build": {
  "win": {
    "target": [
      {
        "target": "nsis",
        "arch": [
          "x64",
          "ia32"
        ]
      }
    ]
  },
  "mac": {
    "target": [
      {
        "target": "dmg",
        "arch": [
          "universal"
        ]
      }
    ]
  }
}
```

electron-builder.yml

```yaml
win:
  target:
    - target: nsis
      arch:
        - x64
        - ia32
mac:
  target:
    - target: dmg
      arch: universal
```

electron-builder.config.js

```javascript
module.exports = {
  "win": {
    "target": [
      {
        "target": "nsis",
        "arch": [
          "x64",
          "ia32"
        ]
      }
    ]
  },
  "mac": {
    "target": [
      {
        "target": "dmg",
        "arch": [
          "universal"
        ]
      }
    ]
  }
}
```

并使用

```
build -wl
```

### 目标配置

- target String - 目标名称。例如 snap。
- arch "x64" | "ia32" | "armv7l" | "arm64" | "universal" - 架构或架构列表。