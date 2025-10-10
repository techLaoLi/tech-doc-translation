# 示例

```javascript
"use strict"

const builder = require("electron-builder")
const Platform = builder.Platform

// 让我们获得智能感知功能
/**
* @type {import('electron-builder').Configuration}
* @see https://www.electron.build/configuration
*/
const options = {
protocols: {
name: "Deeplink Example",
// 不要忘记为 `linux.desktop` 条目设置 `MimeType: "x-scheme-handler/deeplink"`！
schemes: [
"deeplink"
]
},

// "store" | "normal" | "maximum"。- 对于测试构建，使用 'store' 可显著减少构建时间。
compression: "normal",
removePackageScripts: true,

afterSign: async (context) => {
// Mac 发布需要加固+公证：https://developer.apple.com/documentation/xcode/notarizing_macos_software_before_distribution
if (!isDebug && context.electronPlatformName === "darwin") {
await notarizeMac(context)
}
},
artifactBuildStarted: (context) => {
identifyLinuxPackage(context)
},
afterAllArtifactBuild: (buildResult) => {
return stampArtifacts(buildResult)
},
// 如果使用 electron-rebuild 则强制架构构建
beforeBuild: async (context) => {
const { appDir, electronVersion, arch } = context
await electronRebuild.rebuild({ buildPath: appDir, electronVersion, arch })
return false
},
nodeGypRebuild: false,
buildDependenciesFromSource: false,

directories: {
output: "dist/artifacts/local",
buildResources: "installer/resources"
},
files: [
"out"
],
extraFiles: [
{
from: "build/Release",
to: nodeAddonDir,
filter: "*.node"
}
],

win: {
target: 'nsis'
},
nsis: {
deleteAppDataOnUninstall: true,
include: "installer/win/nsis-installer.nsh"
},

mac: {
target: 'dmg',
hardenedRuntime: true,
gatekeeperAssess: true,
extendInfo: {
NSAppleEventsUsageDescription: '让我使用 Apple Events。',
NSCameraUsageDescription: '让我使用摄像头。',
NSScreenCaptureDescription: '让我截屏。',
}
},
dmg: {
background: "installer/mac/dmg-background.png",
iconSize: 100,
contents: [
{
x: 255,
y: 85,
type: "file"
},
{
x: 253,
y: 325,
type: "link",
path: "/Applications"
}
],
window: {
width: 500,
height: 500
}
},

linux: {
desktop: {
StartupNotify: "false",
Encoding: "UTF-8",
MimeType: "x-scheme-handler/deeplink"
},
target: ["AppImage", "rpm", "deb", "pacman"]
},
deb: {
priority: "optional",
afterInstall:"installer/linux/after-install.tpl",
},
rpm: {
fpm: ["--before-install", "installer/linux/before-install.tpl"],
afterInstall:"installer/linux/after-install.tpl",
}
};

// 返回 Promise
builder.build({
targets: Platform.MAC.createTarget(),
config: options
})
.then((result) => {
console.log(JSON.stringify(result))
})
.catch((error) => {
console.error(error)
})
```