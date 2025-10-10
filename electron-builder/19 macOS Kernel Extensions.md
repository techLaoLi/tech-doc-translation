# macOS 内核扩展

使用 electron-builder 安装 macOS 内核扩展可以通过脚本完成。

首先，在 package.json 中，确保您构建的是包（.pkg）而不是默认的 .dmg：

```json
"mac": {
  "target": "pkg"
}
```

将您的脚本和内核扩展放置在 build/pkg-scripts 中，或定义自定义目录。请注意，脚本必须命名为 preinstall 或 postinstall。记住在脚本的第一行使用 #!/bin/sh。此外，您的脚本必须是可执行的（chmod +x <filename>）。

示例脚本：

```bash
#!/bin/sh

echo "卸载和卸载旧扩展..."
# 卸载旧扩展
sudo kextunload /Library/Extensions/myExt.kext

# 删除旧扩展
sudo rm -rf /Library/Extensions/myExtension.kext

# 安装新扩展
echo "安装和加载新扩展..."
sudo cp -R myExt.kext /Library/Extensions/myExt.kext
sudo kextload /Library/Extensions/myExt.kext/
```