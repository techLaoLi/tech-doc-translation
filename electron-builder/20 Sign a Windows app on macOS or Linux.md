# 在 macOS/Linux 上签名 Windows 应用程序

信息

仅当您有 EV 代码签名证书时才需要描述的设置和配置。常规证书开箱即用。

在 Unix 上签名 Windows 应用程序受支持。有多种方法可以实现这一点。基本上您需要一个能够使用 PKCS 11 签名代码的应用程序。有一种方法在 Linux 和 Mac 上都有效，使用 Java。您也可以在两个系统上使用 osslsigncode，对于 Linux 和 Mac 都包含了 how-to。本文档以一些备注和如何将签名过程与 Electron Builder 集成作为结尾。

## 使用 JSign 在 Mac/Linux 上签名 Windows 应用程序

此方法需要 Java。在尝试此解决方案之前，请确保已安装 Java。

1. 通过运行 java -v 确保已安装 Java
2. 下载 JSign
3. 创建一个名为 hardwareToken.cfg 的文件。用下面的内容填充它。
4. 检查库链接以确保您有正确的 PKCS 模块。此链接可能因令牌而异。在 Linux 上您会在 /lib 中找到它，而在 Mac 上您可以在 /Library/Frameworks 或 /usr/local/lib 中找到它。
5. 为 Mac 安装令牌驱动程序，导出证书（如果它是 .cer 则转换为 pem）
6. 运行 java -jar jsign-2.1.jar 并使用正确的参数。

hardwareToken.cfg

```cfg
name = HardwareToken
library = /Library/Frameworks/eToken.framework/Versions/A/libeToken.dylib
slotListIndex = 0
```

URL：
- GitHub 上的 JSign

签名的完整命令：

```bash
java -jar jsign-2.1.jar --keystore hardwareToken.cfg --storepass "your password here" --storetype PKCS11 --tsaurl http://timestamp.digicert.com --alias /link/to/cert.pem /link/to/app.exe
```

## 使用 osslsigncode 在 Mac 上签名 Windows 应用程序

主要问题是 brew 中缺少支持 OpenSSL 1.1 的引擎 PKCS 11。当前版本仅支持 OpenSSL 1.0。因此您需要自己编译大部分应用程序。

1. 使用 brew 安装一些应用程序，如 autoconf、automake、libtool、pkg-config 和 gnu-tar。
2. 创建文件夹如 /usr/local/mac-dev，给它当前用户的权限，在编译期间使用该文件夹作为前缀。
3. 下载 OpenSSL 1.1.1 tar.gz，参见下面的链接，解压并编译：./config --prefix=/usr/local/mac-dev && make && make install
4. 导出 OpenSSL 1.1 变量用于编译下面的应用程序
5. export LDFLAGS="-L/usr/local/mac-dev/lib"
6. export CPPFLAGS="-I/usr/local/mac-dev/include"
7. export PATH="/usr/local/mac-dev/bin:$PATH"
8. export PKG_CONFIG_PATH="/usr/local/mac-dev/lib/pkgconfig"
9. 安装 opensc (.dmg 文件)
10. 下载 libp11 .tar.gz，解压并编译：./configure --prefix=/usr/local/mac-dev && make && make install
11. 为 Mac 安装令牌驱动程序，导出证书（如果它是 .cer 则转换为 pem）
12. 下载 osslsigncode .tar.gz，解压并编译：./autogen.sh && ./configure --prefix=/usr/local/mac-dev && make && make install（之后将二进制文件链接到 /usr/local/bin）
13. 通过运行 pkcs11-tool --module /usr/local/lib/libeTPkcs11.dylib -l -O 找出密钥 ID
14. 使用正确的模块、引擎和密钥 ID 运行 osslsigncode

URL：
- OpenSSL 1.1 来自他们的网站
- GitHub 上的 OpenSC
- GitHub 上的 Libp11
- GitHub 上的 osslsigncode

签名的完整命令，pkcs11module 参数可能因令牌而异。

```bash
osslsigncode sign -verbose -pkcs11engine /usr/local/mac-dev/lib/engines-1.1/libpkcs11.dylib -pkcs11module /usr/local/lib/libeTPkcs11.dylib -h sha256 -n app-name -t https://timestamp.verisign.com/scripts/timestamp.dll -certs /link/to/cert.pem -key 'key-id-here' -pass 'password' -in /link/to/app.exe -out /link/to/app.signed.exe
```

## 使用 osslsigncode 在 Ubuntu 18.04 上签名 Windows 应用程序

这些步骤也适用于其他 Linux 操作系统，但包的名称可能不同。

1. 使用 APT 更新包。sudo apt-get update。
2. 使用 APT 安装包 sudo apt-get install -y openssl libcurl4-openssl-dev libssl-dev libengine-pkcs11-openssl curl libcurl4 git automake libtool pkg-config wget libccid libpcsclite1 pcscd usbutils opensc。
3. 下载 osslsigncode .tar.gz，解压并编译：./autogen.sh && ./configure && make && make install
4. 为 Linux 安装令牌驱动程序，导出证书（如果它是 .cer 则转换为 pem）
5. 通过运行 pkcs11-tool --module /lib/libeToken.so -l -O 找出密钥 ID。检查路径以确保您有正确的 PKCS 模块。此路径可能因令牌而异。
6. 使用正确的模块、引擎和密钥 ID 运行 osslsigncode

签名的完整命令：

```bash
osslsigncode sign -verbose -pkcs11engine /usr/lib/x86_64-linux-gnu/engines-1.1/pkcs11.so -pkcs11module /lib/libeToken.so -h sha256 -n app-name -t https://timestamp.verisign.com/scripts/timestamp.dll -certs /link/to/cert.pem -key 'key-id-here' -pass 'password' -in /link/to/app.exe -out /link/to/app.signed.exe
```

URL：
- GitHub 上的 osslsigncode

## 备注

当出现问题时，请考虑以下几点。

- 确保您使用正确的 PKCS 11 引擎和模块。如果您收到未找到带有令牌的插槽或一些错误，如 sc 连接卡错误和卡无效或无法处理，说明您没有使用正确的模块，请确保您使用正确的模块。
- 如果您自己编译了 OpenSSL，请确保您使用的是也为 OpenSSL 编译的引擎。否则您将遇到兼容性问题。
- 使用此处列出的 URL 中的 osslsigncode。有更多分支/版本可以找到。这里包含的是实际维护的库，需要 OpenSSL 1.1。

## 与 Electron Builder 集成签名

根据您选择的方法，您可以通过创建 sign.js 文件来自动化签名过程。然后在您的 package.json 中指向此文件。

package.json

```json
...
"win": {
  "target": "nsis",
  "signtoolOptions": {
    "sign": "./sign.js"
  }
},
...
```

sign.js

```javascript
exports.default = async function(configuration) {
  // 不要在文件中包含密码或其他敏感数据
  // 而是创建带有敏感数据的环境变量
  const CERTIFICATE_NAME = process.env.WINDOWS_SIGN_CERTIFICATE_NAME;
  const TOKEN_PASSWORD = process.env.WINDOWS_SIGN_TOKEN_PASSWORD;

  require("child_process").execSync(
    // 您的命令在这里！例如使用 JSign：
    `java -jar jsign-2.1.jar --keystore hardwareToken.cfg --storepass "${TOKEN_PASSWORD}" --storetype PKCS11 --tsaurl http://timestamp.digicert.com --alias "${CERTIFICATE_NAME}" "${configuration.path}"`,
    {
      stdio: "inherit"
    }
  );
};
```