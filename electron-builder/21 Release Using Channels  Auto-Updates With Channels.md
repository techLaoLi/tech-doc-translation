# 使用渠道发布

## 描述

渠道对于向选定的用户分发应用程序的"beta"或"alpha"版本非常有用。这允许在将应用程序作为"latest"（稳定版）发布之前进行测试。

接收"beta"版本的用户也将获得"latest"版本。否则，不想要"beta"的用户将只获得"latest"版本。

有三个渠道，按稳定性排序：

1. "latest"，您的应用程序是稳定的，这是默认的（例如：1.3.2），
2. "beta"，这意味着您的应用程序可以工作，但应该有一些错误（例如：1.3.2-beta）
3. "alpha"，这意味着您的应用程序不稳定且正在积极开发中（例如：1.3.2-alpha）

### 配置

要使用渠道发布，您应该配置 electron-builder 并在客户端定义要使用的渠道。

### Electron-Builder

默认情况下（不使用渠道），所有应用程序版本都使用"latest"渠道。

如果您想使用渠道，应该在 package.json 中添加以下内容：

```json
"version": "x.x.x-beta",
...
"build": {
  "generateUpdatesFilesForAllChannels": true,
  ...
}
```

注意

当 generateUpdatesFilesForAllChannels = true 时，allowDowngrade 会自动设置为 true，因此您无需设置它。

要使用渠道发布，您只需在 package.json 的版本标签中定义渠道。添加"-beta"或"-alpha"（"latest"不需要任何内容）即可自动为相关渠道构建。

警告

即使 detectUpdateChannel 为 true，Github 发布也不会遵循版本标签中的版本。您必须显式设置渠道以匹配预期的发布渠道。

### 您的应用程序

您需要做的就是在客户端定义用户将接收哪个渠道：

autoUpdater.channel = "beta"（请参见此处的文档）

根据定义的渠道，将向用户分发以下版本：

- latest 或 nothing：用户将只获得"latest"版本
- beta：用户将获得"beta"和"latest"版本
- alpha：用户将获得"alpha"、"beta"和"latest"版本

## 如何使用它

假设您的应用程序是稳定的，版本为 1.0.1。

如果您想为新的 1.1.0 版本发布 beta 版本，您只需将 package.json 版本更新为 1.1.0-beta。

当您的应用程序足够稳定时，您想将其发布给所有用户。为此，您只需从 package.json 版本标签中删除 -beta 标签。