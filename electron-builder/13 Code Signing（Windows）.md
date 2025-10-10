# Windows

Windows 代码签名受支持。如果在 package.json 中正确提供了配置值，则签名应自动执行。

提示

Windows 使用双代码签名（SHA1 和 SHA256 哈希算法）。

要在 Windows 上签名应用程序，有两种类型的证书：

- EV 代码签名证书
- 代码签名证书

两种证书都适用于自动更新。常规的（通常更便宜的）代码签名证书在安装期间显示警告，一旦足够多的用户安装了您的应用程序并建立了信任，警告就会消失。EV 证书具有更多信任，因此可以立即工作而没有任何警告。但是，无法导出 EV 证书，因为它绑定到物理 USB 令牌。因此，您无法导出证书以在 CI（如 AppVeyor）上签名代码。

如果您使用 EV 证书，您需要在 electron-builder 配置中提供 win.certificateSubjectName。

如果您使用 Windows 7，请确保 PowerShell 已更新到版本 3.0。

如果您在 Linux 或 Mac 上，并且想要使用 EV 代码签名证书签名 Windows 应用程序，请使用 Unix 系统指南。

## 使用 Azure 可信签名（测试版）

Microsoft 本身提供了一个名为 Azure Trusted Signing 的代码签名服务，您可以使用它来对应用程序进行代码签名。

如果您还没有 Azure 设置并且只想使用其代码签名服务，请使用此快速入门指南设置 Azure"可信签名帐户"。然后，在 Azure 中设置"应用注册"，按照步骤为其创建"机密"，并将"可信签名证书配置文件签名者"角色分配给应用注册。

要使用您的证书进行签名，您需要调整 electron-builder 的配置并设置用于身份验证的环境变量。环境变量由 Invoke-TrustedSigning 模块直接读取；它们不由 electron-builder 解析或解析。

首先，要指导 electron-builder 使用 Azure Trusted Signing，您需要在 electron-builder 配置中设置 win.azureSignOptions 属性。按照 Microsoft 的说明进行配置。

| 属性 | 描述 |
| --- | --- |
| publisherName | 这必须与您希望使用的证书的 CommonName (CN) 属性完全匹配。 |
| endpoint | 这对应于您在创建证书时选择的端点。 |
| certificateProfileName | 可信签名帐户中的证书配置文件名称。 |
| codeSigningAccountName | 这是可信签名帐户的名称（请注意，这不是应用注册的帐户名称）。 |

可以在 win.azureSignOptions 下提供其他字段，这些字段直接传递给 Invoke-TrustedSigning powershell 模块。

其次，为构建操作提供适当的环境变量。可以在 Azure.Identity 类 - EnvironmentCredential 类中找到每个变量的描述。您只需要提供表中列出的与您选择使用的身份验证方法相对应的环境变量。

提示

如果您使用上面描述的使用"应用注册"的最小设置，则"使用机密的服务主体"适用于您。在这种情况下，您只需要租户 ID、客户端 ID 和客户端机密。

| 环境变量名 | 描述 |
| --- | --- |
| AZURE_TENANT_ID | 您的 Azure AD 租户 ID；可以在 Entra ID 门户中找到。 |
| AZURE_CLIENT_ID | 您的"应用注册"的应用程序（客户端）ID。请注意，这不是"对象"ID。 |
| AZURE_CLIENT_SECRET | 您为应用注册创建的"机密"的值。请注意，这不是机密的 ID。 |
| AZURE_CLIENT_CERTIFICATE_PATH | 如果您自带证书，则为必需。 |
| AZURE_CLIENT_SEND_CERTIFICATE_CHAIN | 如果您自带证书，则为必需。 |
| AZURE_USERNAME | 您的 Microsoft Entra 帐户的用户名。 |
| AZURE_PASSWORD | 您的 Microsoft Entra 帐户的密码。 |