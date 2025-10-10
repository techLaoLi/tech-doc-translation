# 在本地测试 S3 更新

# 描述

自动更新是任何应用程序最关键的组成部分之一，测试该过程很重要。

此页面旨在解释如何在本地测试自动更新过程。

更新步骤大部分复制自这里

# 步骤

## 1. 下载并安装 Minio

Minio 是一个可本地运行的服务器，实现了 S3 协议。https://min.io/download

下载服务器和客户端并将它们放在您想要的目录中。对于本文档，我们将此目录称为 minio-home

您的目录应如下所示：

```
minio-home
├── minio.exe
└── mc.exe
```

## 2. 创建并配置存储桶

在 minio-home 中运行以下命令：

```
mkdir ./minio-data
mkdir ./minio-data/test-bucket
```

然后您可以运行 `./minio.exe server ./minio-data` 来启动服务器。这将使用默认凭据（即 minioadmin）启动服务器。

在 http://127.0.0.1:9000 访问 Web 客户端 - 您应该能够使用默认凭据访问。

此时您应该在存储桶上添加读取策略。您可以通过访问 Web 客户端，进入存储桶设置，然后添加 * 只读策略来实现。

> 这对于更新器访问更新是必要的。
> {.is-info}

## 3. 发布更新

首先构建一次应用程序，这样我们就无需等待每次构建。为此，请先运行您的编译步骤。

然后确保您的 AWS_ACCESS_KEY_ID 和 AWS_SECRET_ACCESS_KEY 环境变量已设置。您可以在终端会话或您的 IDE（如 Webstorm）中设置它们，或者您可以包含它们与 dotenv，然后在配置文件顶部添加 require("dotenv").config()（如果它是一个 javascript 文件）。

设置 env 变量后，您可以运行带有修改的发布命令。
例如，您有一个名为 publish:prod 的命令，它是 electron-builder --x64 --config configs/electron-builder.js，

您将创建 publish:dev，它是 yarn publish:prod --publish always --config.publish.provider=s3 --config.publish.endpoint=http://localhost:9000 --config.publish.bucket=test-update

这将自动覆盖发布配置并运行发布过程。

然后进入您的 package.json 文件并将版本号更新为高于当前版本的版本。这对于检测新更新很重要。

如果您现在进入 Web 客户端，您应该看到可执行文件、相应的 blockmap 和 latest.yml 文件。

## 4. 安装和测试

现在您想要安装构建的应用程序。它应该存在于您的配置中指定的输出目录中（或默认的 dist 目录）。

启动应用程序并检查日志输出。在 Windows 中，它应该在此目录中：%AppData%\Roaming\yourapp\log.log 这将包含更新器日志，并指示更新失败的位置或步骤（如果失败）。

如果您的更新流程正确，那么您应该会看到正常的更新通知。