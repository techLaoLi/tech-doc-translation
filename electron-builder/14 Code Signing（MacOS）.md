# MacOS

macOS 代码签名受支持。如果在 package.json 中正确提供了配置值，则签名应自动执行。

在 macOS 开发机器上，将自动使用您钥匙串中的有效且适当的标识。如果不存在此类标识，则默认行为取决于目标架构。在 ARM 或通用构建上，默认情况下将应用临时签名。在仅 Intel 构建上，默认行为是根本不签名。

提示

请参见文章对您的 Electron 应用程序进行公证。

## 如何在 macOS 上导出证书

1. 打开钥匙串。
2. 选择登录钥匙串和我的证书类别。
3. 选择所有必需的证书（提示：使用 cmd-click 选择多个）：
4. Developer ID Application：为 macOS 签名应用程序。
5. 3rd Party Mac Developer Installer：和 Apple Distribution 或 3rd Party Mac Developer Application：为 MAS（Mac App Store）签名应用程序。
6. Developer ID Application：和 Developer ID Installer 为分发到 Mac App Store 之外签名应用程序和安装程序。
7. Apple Development：或 Mac Developer：为测试 Mac App Store 提交（mas-dev 目标）签名开发构建。您还需要在工作目录中有一个与该证书和用于测试的设备匹配的配置文件。

请注意——您可以选择任意数量的证书。electron-builder 方面没有限制。
所有选定的证书都将导入到 CI 服务器上的临时钥匙串中。
4. 打开上下文菜单并导出。

## 如何在 macOS 上构建过程中禁用代码签名

要在 macOS 上构建时禁用代码签名，请将除 CSC_IDENTITY_AUTO_DISCOVERY 之外的所有上述变量保持未设置状态，CSC_IDENTITY_AUTO_DISCOVERY 需要设置为 false。这可以通过运行 export CSC_IDENTITY_AUTO_DISCOVERY=false 来完成。

另一种方法——将 mac.identity 设置为 null。您也可以使用 CLI 传递附加配置：-c.mac.identity=null。如果您为 ARM 构建，您可能实际上想要使用临时签名，在这种情况下，您应该将 mac.identity 设置为 -。

## 代码签名和公证教程

感谢社区成员整理了这些内容。