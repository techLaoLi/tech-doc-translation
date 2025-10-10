# 图标

推荐工具：AppIcon Generator, MakeAppIcon, iConvert Icons。

## macOS

文件

- 可选的 icon.icns（macOS 应用图标）或 icon.png。图标大小至少应为 512x512。
- 可选的 background.png（macOS DMG 背景）。
- 可选的 background@2x.png（macOS DMG Retina 背景）。

需要放置在 buildResources 目录中（默认为 build）。所有文件都是可选的——但提供 icon.icns（或 icon.png）很重要，否则将使用默认的 Electron 图标。

## Windows (NSIS)

- 可选的 icon.ico（Windows 应用图标）或 icon.png。图标大小至少应为 256x256。

需要放置在 buildResources 目录中（默认为 build）。提供 icon.ico（或 icon.png）很重要，否则将使用默认的 Electron 图标。

## Linux

Linux 图标集将基于 macOS icns 文件或通用 icon.png 自动生成。

或者您可以将它们放入 build/icons 目录中，如果您想自己指定它们。
文件名必须包含图标的大小（例如 256x256.png）。推荐大小：16, 32, 48, 64, 128, 256（或仅 512）。

## AppX

请参见 AppX Assets。