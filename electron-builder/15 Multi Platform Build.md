# 多平台构建

信息

不要期望您可以在一个平台上为所有平台构建应用程序。

- 如果您的应用程序有本地依赖项，则只能在目标平台上编译，除非使用 prebuild。
  prebuild 是一个解决方案，但大多数 node 模块不提供预构建二进制文件。
- macOS 代码签名仅在 macOS 上工作。无法修复。

免费的公共 Electron 构建服务用于在 Windows 上为 Linux 构建 Electron 应用程序。在 macOS/Linux 上，您可以在本地为 Windows 构建 Electron 应用程序，除了用于 Windows Store 的 Appx（将来（随时提交问题）electron-build-service 将支持 Appx 目标）。

您可以使用构建服务器——例如 Travis 来构建 macOS/Linux 应用程序，AppVeyor 来构建 Windows 应用程序。

默认情况下为当前平台和当前架构构建。使用 CLI 标志 --mac、--win、--linux 指定平台。以及 --ia32、--x64 指定架构。

例如，为 macOS、Windows 和 Linux 构建应用程序：

```bash
electron-builder -mwl
```

并行执行构建，因此强烈建议不要为每个平台使用 npm 任务（例如 npm run dist:mac && npm run dist:win32），而是在一个构建命令中指定多个平台/目标。
您无需在构建前清理 dist 输出——输出目录会自动清理。

## 用于为 macOS、Linux 和 Windows 构建 Electron 应用程序的示例 .travis.yml

示例 .travis.yml

```yaml
matrix:
  include:
    - os: osx
      osx_image: xcode10.2
      language: node_js
      node_js: "10"
      env:
        - ELECTRON_CACHE=$HOME/.cache/electron
        - ELECTRON_BUILDER_CACHE=$HOME/.cache/electron-builder

    - os: linux
      services: docker
      language: generic

cache:
  directories:
    - node_modules
    - $HOME/.cache/electron
    - $HOME/.cache/electron-builder

script:
  - |
    if [ "$TRAVIS_OS_NAME" == "linux" ]; then
      docker run --rm \
        --env-file <(env | grep -iE 'DEBUG|NODE_|ELECTRON_|YARN_|NPM_|CI|CIRCLE|TRAVIS|APPVEYOR_|CSC_|_TOKEN|_KEY|AWS_|STRIP|BUILD_') \
        -v ${PWD}:/project \
        -v ~/.cache/electron:/root/.cache/electron \
        -v ~/.cache/electron-builder:/root/.cache/electron-builder \
        electronuserland/builder:wine \
        /bin/bash -c "yarn --link-duplicates --pure-lockfile && yarn release --linux --win"
    else
      yarn release
    fi
before_cache:
  - rm -rf $HOME/.cache/electron-builder/wine

branches:
  except:
    - "/^v\\d+\\.\\d+\\.\\d+$/"
```

## 用于为 Windows 构建 Electron 应用程序的示例 appveyor.yml

仅在以下情况下使用 AppVeyor：
* 您需要构建 AppX，
* 或您的应用程序有本地依赖项且未提供预构建二进制文件。

否则请参见上面的示例 .travis.yml，使用提供的 Docker 镜像在 Linux 上构建 Windows。

示例 appveyor.yml

```yaml
image: Visual Studio 2017

platform:
  - x64

cache:
  - node_modules
  - '%USERPROFILE%\.electron'

init:
  - git config --global core.autocrlf input

install:
  - ps: Install-Product node 10 x64
  - yarn

build_script:
  - yarn dist

test: off
```

## macOS

所有必需的系统依赖项（rpm 除外）将在 macOS 10.12+（macOS Sierra）上按需自动下载。在 Travis 上，请添加 osx_image: xcode10.2（请参见上面的示例 .travis.yml）。

要构建 rpm：brew install rpm (brew)。

## Linux

您可以使用 Docker 来避免安装系统依赖项。

要为 Linux 构建可分发格式的应用程序：

```bash
sudo apt-get install --no-install-recommends -y libopenjp2-tools
```

要构建 rpm：sudo apt-get install --no-install-recommends -y rpm（或 sudo yum install rpm-build）。

要构建 pacman：sudo apt-get install --no-install-recommends -y bsdtar。

要构建 snap（当且仅当您有自定义阶段包时（如果您没有自定义 snap 构建配置，则无需安装））。请参见 Store 中的 snapcraft。

```bash
sudo snap install snapcraft --classic
sudo snap install multipass --beta --classic
```

### 在 Linux 上为 Windows 构建应用程序：

建议使用 Docker（electronuserland/builder:wine）来避免安装系统依赖项。

- 安装 Wine（需要 2.0+）——请参见 WineHQ 二进制包。
- 如果要使用 Squirrel.Windows（NSIS，默认目标，不需要 mono），请安装 Mono（需要 4.2+）。

### 从 64 位机器构建 32 位应用程序：

```bash
sudo apt-get install --no-install-recommends -y gcc-multilib g++-multilib
```

## Travis Linux

需要 Xenial。

```bash
sudo: required
dist: xenial
```

### Travis macOS

需要 macOS 10.14+。

```bash
osx_image: xcode10.2
```

## Docker

在任何平台上构建 Linux 或 Windows。

警告

如果您有本地依赖项且本地依赖项不使用 prebuild，则无法使用 Docker 为 Windows 构建。

请参见示例 .travis.yml 中 CI 服务器上的 Docker 用法。

注意

在 macOS 上不要使用 Docker Toolbox。只有 Docker for Mac 可以工作。

### 在本地机器上使用 Docker 构建 Electron 应用程序

1. 运行 docker 容器：
   ```bash
   docker run --rm -ti \
     --env-file <(env | grep -iE 'DEBUG|NODE_|ELECTRON_|YARN_|NPM_|CI|CIRCLE|TRAVIS_TAG|TRAVIS|TRAVIS_REPO_|TRAVIS_BUILD_|TRAVIS_BRANCH|TRAVIS_PULL_REQUEST_|APPVEYOR_|CSC_|GH_|GITHUB_|BT_|AWS_|STRIP|BUILD_') \
     --env ELECTRON_CACHE="/root/.cache/electron" \
     --env ELECTRON_BUILDER_CACHE="/root/.cache/electron-builder" \
     -v ${PWD}:/project \
     -v ${PWD##*/}-node-modules:/project/node_modules \
     -v ~/.cache/electron:/root/.cache/electron \
     -v ~/.cache/electron-builder:/root/.cache/electron-builder \
     electronuserland/builder:wine
   ```
2. 输入 yarn && yarn dist

如果您在 package.json 中没有 dist npm 脚本，请直接调用 ./node_modules/.bin/electron-builder。

或者为了避免第二步，将 /bin/bash -c "yarn && yarn dist" 追加到第一个命令。您可以使用 /test.sh 安装依赖项并运行测试。

提示

如果您不需要构建 Windows，请使用镜像 electronuserland/builder（此镜像中未安装 wine）。

### 提供的 Docker 镜像

提示

最好将您的 FROM 锁定到特定的日期标签（例如 builder:18-07.23）或 sha，而不是使用 latest 这样的标签，因为工具集可能会升级，例如升级 node 版本。

- electronuserland/builder 或 electronuserland/builder:20 — NodeJS 20 和必需的系统依赖项。基于 builder:base。如果您只需要构建 Linux 目标，请使用此镜像。
- electronuserland/builder:wine — Wine、NodeJS 20 和必需的系统依赖项。基于 builder:20。如果您需要构建 Windows 目标，请使用此镜像。
- electronuserland/builder:wine-mono — 用于 Squirrel.Windows 的 Mono。基于 builder:wine。如果您需要构建 Squirrel.Windows 目标，请使用此镜像。
- electronuserland/builder:wine-chrome — google-chrome-stable 和 xvfb 可用 — 您可以使用此镜像进行 Electron 应用程序的无头测试。基于 builder:wine。
- electronuserland/builder:base — 必需的系统依赖项。不建议直接使用。

还有这些 docker 镜像的 Node 14/16/18 版本（builder:14、builder:14-wine 等）
Docker 镜像也使用日期后缀 -%m.%y 标记，例如：builder:18-07.23。
完整的 docker 构建脚本可以在这里找到：build.sh
正在构建的 Node 版本：test.yaml