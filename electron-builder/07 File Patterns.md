# 文件模式

- * 匹配路径部分中的 0 个或多个字符
- ? 匹配 1 个字符
- [...] 匹配一个字符范围，类似于 RegExp 范围。
  如果范围的第一个字符是 ! 或 ^，则它匹配
  范围中未包含的任何字符。
- !(pattern|pattern|pattern) 匹配与提供的任何模式都不匹配的内容
- ?(pattern|pattern|pattern) 匹配提供的模式的零次或一次出现
- +(pattern|pattern|pattern) 匹配提供的模式的一次或多次出现
- *(a|b|c) 匹配提供的模式的零次或多次出现
- @(pattern|pat*|pat?erN) 精确匹配提供的模式之一
- ** 如果"globstar"在路径部分中单独存在，则它匹配
  零个或多个目录和子目录以寻找匹配项。
  它不会爬取符号链接目录。

如果匹配到目录，则会复制所有内容。因此，您可以只指定 foo 来复制 foo 目录。

排除目录

请记住，!doNotCopyMe/**/* 会匹配 doNotCopyMe 目录中的文件，但不会匹配目录本身，因此会创建空目录。
解决方案——使用宏 ${/*}，例如 !doNotCopyMe${/*}。

## 多个 Glob 模式

```json
[
  // 匹配所有文件
  "**/*",

  // 除了 foo/ 目录中的 js 文件
  "!foo/*.js",

  // 除非是 foo/bar.js
  "foo/bar.js",
]
```

## 文件宏

您可以在文件模式、构件文件名模式和发布配置 url 中使用宏：

- ${arch} — 展开为 ia32, x64。如果没有 arch，宏将从您的模式中删除，包括前导空格、- 和 _（因此，您无需担心，可以重复使用模式）。
- ${os} — 根据目标平台展开为 mac, linux 或 win。
- ${platform} — 根据 Node.js process.platform 属性展开为 darwin, linux 或 win32。
- ${name} – package.json 名称。
- ${productName} — 经过清理的产品名称。
- ${version}
- ${channel} — 从版本中检测到的预发布组件（例如 beta）。
- ${env.ENV_NAME} — 任何环境变量。
- AppInfo 的任何属性（例如 buildVersion, buildNumber）。