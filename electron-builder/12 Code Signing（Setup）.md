# 设置

macOS 和 Windows 代码签名受支持。如果在 package.json 中正确提供了配置值，则签名应自动执行。

| 环境变量名 | 描述 |
| --- | --- |
| CSC_LINK | 证书的 HTTPS 链接（或 base64 编码的数据，或 file:// 链接，或本地路径）。支持简写 ~/（主目录）。 |
| CSC_KEY_PASSWORD | 解密 CSC_LINK 中给定证书的密码。 |
| CSC_NAME | 仅限 macOS 从登录钥匙串中检索的证书名称。在开发机器上（不在 CI 上）有用，如果您有几个身份（否则不要指定它）。 |
| CSC_IDENTITY_AUTO_DISCOVERY | true 或 false。默认为 true — 在 macOS 开发机器上将自动使用您钥匙串中的有效且适当的标识。 |
| CSC_KEYCHAIN | 钥匙串名称。如果未指定 CSC_LINK，则使用。默认为系统默认钥匙串。 |

提示

如果您将应用程序包装到安装程序（pkg）中，则需要在您的钥匙串中有 INSTALLER ID 身份，或提供相应的 CSC_INSTALLER_LINK 和 CSC_INSTALLER_KEY_PASSWORD。

提示

如果您在 macOS 上构建 Windows 并需要设置不同的证书和密码（与 CSC_* 环境变量中设置的不同），您可以使用 WIN_CSC_LINK 和 WIN_CSC_KEY_PASSWORD。

## Travis、AppVeyor 和其他 CI 服务器

要在构建服务器上签名应用程序，您需要设置 CSC_LINK, CSC_KEY_PASSWORD：

1. 导出证书。
   考虑不要在密码中使用特殊字符（对于 bash[1]），因为"执行构建时不会转义值"。
2. 将文件编码为 base64（macOS: base64 -i yourFile.p12 -o envValue.txt, Linux: base64 yourFile.p12 > envValue.txt）。

或上传 *.p12 文件（例如，在 Google Drive 上，使用直接链接生成器获取正确的下载链接）。

1. 设置 CSC_LINK 和 CSC_KEY_PASSWORD 环境变量。请参见 Travis 或 AppVeyor 文档。
   建议在 CI 项目设置中设置，而不是在 .travis.yml/appveyor.yml 中。如果您使用文件链接（不是 base64 编码的数据），请确保相应地转义特殊字符（对于 bash[1]）。

在 AppVeyor 的情况下，不要忘记点击锁图标来"切换变量加密"。

请记住，Windows 无法处理超过 8192 个字符的环境变量值，因此如果您的证书的 base64 表示超过该限制，请尝试重新导出证书而不包括认证路径中的所有证书（它们不是必需的，但证书管理器导出向导默认勾选该选项），否则编码值将被截断。

[1] printf "%q\n" "<url>"

## 在哪里购买代码签名证书

请参见获取 Windows 代码签名证书（平台："Microsoft Authenticode"）。
请注意——Gatekeeper 仅识别 Apple 数字证书。

## 代码签名的替代方法

通过 Electron Builder 的配置（通过 package.json）进行代码签名并不是签名应用程序的唯一方法。有些人发现使用 GUI 工具进行代码签名更容易。几个示例包括：
- SSL manager
- DigiCert 用于 Windows 的实用程序
当然，对此类工具的全面讨论超出了本文档的范围。