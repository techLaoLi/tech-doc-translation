# 发布

publish 键包含一组选项，用于指示 electron-builder 如何发布构件以及如何为自动更新构建更新信息文件。

String | Object | Array<Object | String>，其中 Object 是 Keygen、通用服务器、GitHub、S3、Spaces 或 Snap Store 选项。顺序很重要——第一项将用作默认的自动更新服务器。可以在顶层配置或任何平台（mac、linux、win）或目标（例如 nsis）特定配置中指定。

请注意，当使用通用服务器时，您必须自己上传构建的应用程序和元数据文件。

Travis 和 AppVeyor 支持发布构件。但这需要为每个 CI 进行额外配置，您需要配置要发布的内容。
electron-builder 使发布变得非常简单。

如果定义了 GH_TOKEN 或 GITHUB_TOKEN —— 默认为 [{provider: "github"}]。

如果定义了 KEYGEN_TOKEN 且未定义 GH_TOKEN 或 GITHUB_TOKEN —— 默认为 [{provider: "keygen"}]。

如果定义了 GITHUB_RELEASE_TOKEN，它将用于发布您的版本，而不是 (GH_TOKEN 或 GITHUB_TOKEN)。
- 例如，mac: export GITHUB_RELEASE_TOKEN=<my token>
- GITHUB_TOKEN 仍将在您的应用程序检查更新时使用等。
- 在创建细粒度个人访问令牌时，您可以将 GITHUB_TOKEN 设置为"只读"，而 GITHUB_RELEASE_TOKEN 设置为"读写"。
- "Contents"细粒度权限已足够。（撰写时 - 2024年4月）

Snap store

snap 目标默认发布到 snap store（Linux 的应用商店）。要强制发布到其他提供商，请为 snap 显式指定发布配置。

您可以发布到多个提供商。例如，将 Windows 构件发布到 GitHub 和 Bitbucket（顺序很重要——第一项将用作默认的自动更新服务器，因此在此示例中，应用程序将使用 github 作为自动更新提供商）：

```json
{
  "build": {
    "win": {
      "publish": ["github", "bitbucket"]
    }
  }
}
```

```yaml
win:
  publish:
    # 一个带有附加选项的 github 提供商对象
    - provider: github
      protocol: https
    # 一个将使用默认选项的 bitbucket 字符串提供商
    - bitbucket
```

您还可以使用 CLI 参数配置发布，例如，强制将 snap 发布到 GitHub 而不是 Snap Store：-c.snap.publish=github

如果需要，可以使用自定义发布提供商。

宏

所有发布选项都支持文件宏。

## 如何发布

摘自 electron-builder 命令的 CLI 用法：

```bash
Publishing:
--publish, -p  [choices: "onTag", "onTagOrDraft", "always", "never"]
```

CLI `--publish` 选项值：
| 值 | 描述 |
| --- | --- |
| onTag | 仅在推送标签时 |
| onTagOrDraft | 在推送标签或存在草稿版本时 |
| always | 总是发布 |
| never | 从不发布 |

但请考虑使用自动规则而不是显式指定发布：

- 如果检测到 CI 服务器，—— onTagOrDraft。
- 如果 CI 服务器报告已推送标签，—— onTag。

只有在推送标签时才会起草（如果尚不存在）并发布构件。

- 如果 npm 脚本名为 release，—— always。

在开发 package.json 中添加到脚本：

```json
"release": "electron-builder"
```

如果您运行 yarn release，将起草（如果尚不存在）并发布构件。

### 推荐的 GitHub Releases 工作流程

1. 起草一个新版本。将"标签版本"设置为应用程序 package.json 中的版本值，并以 v 为前缀。"发布标题"可以是任何您想要的内容。
   例如，如果您的应用程序 package.json 版本是 1.0，您的草稿"标签版本"将是 v1.0。
2. 推送一些提交。每次 CI 构建都会更新附加到此草稿的构件。
3. 完成后，发布该版本。GitHub 将为最新提交打标签。

此工作流程的好处是它允许您始终拥有最新的构件，并且可以在准备好后发布版本。

### Amazon S3 和其他非 GitHub 上的持续部署工作流程

此示例工作流程是基于 maven 中处理发布的方式建模的（这是许多可能工作流程中的一个示例，您不必强制遵循它）。

1. 设置您的 CI 以在每次提交时发布。例如，在您的 package.json 中添加 "dist": "electron-builder --publish always"。
2. 将您的应用程序 package.json 中的版本设置为 1.9.0-snapshot（或 1.9.0-master 或您希望命名的任何开发渠道）。这将发布一个名为 snapshot.yml 的文件和一个名为 something-snapshot.exe 的构建（以及相应的 mac 版本）到 S3。
3. 当您准备好部署时，只需将包版本更改为 1.9.0 并推送。这将生成 latest.yml 和 something.exe 到 s3。通常您还会为此版本打 git 标签（只是为了跟踪它）。
4. 在此之后立即将版本改回快照版本，即 1.10.0-snapshot，并提交。

## GitHub 仓库

使用以下方式自动检测：

- 应用程序或开发 package.json 中的 repository，
- 如果未设置，则使用环境变量
  TRAVIS_REPO_SLUG
  或 APPVEYOR_REPO_NAME
  或 CIRCLE_PROJECT_USERNAME/CIRCLE_PROJECT_REPONAME,
- TRAVIS_REPO_SLUG
- 或 APPVEYOR_REPO_NAME
- 或 CIRCLE_PROJECT_USERNAME/CIRCLE_PROJECT_REPONAME,
- 如果没有环境变量，则从 .git/config origin url 检测。

# 发布商

## Bitbucket

Electron-Builder / builder-util-runtime / BitbucketOptions

Bitbucket 选项。
https://bitbucket.org/
定义 BITBUCKET_TOKEN 环境变量。

要将应用密码转换为可用令牌，您可以使用以下方法：

```javascript
convertAppPassword(owner: string, appPassword: string) {
  const base64encodedData = Buffer.from(`${owner}:${appPassword.trim()}`).toString("base64")
  return `Basic ${base64encodedData}`
}
```

#### 扩展自

- PublishConfiguration

#### 属性

##### channel?

> readonly optional channel: null | string

渠道。

###### 默认值

```json
latest
```

---

##### owner

> readonly owner: string

仓库所有者

---

##### provider

> readonly provider: "bitbucket"

提供商。必须是 bitbucket。

###### 覆盖

PublishConfiguration.provider

---

##### publishAutoUpdate?

> readonly optional publishAutoUpdate: boolean

是否发布自动更新信息文件。

自动更新仅依赖于列表中的第一个提供商（您可以指定多个发布商）。
因此，可能不需要为其他配置的提供商上传元数据文件。但默认情况下会上传。

###### 默认值

```json
true
```

###### 继承自

PublishConfiguration.publishAutoUpdate

---

##### requestHeaders?

> readonly optional requestHeaders: OutgoingHttpHeaders

任何自定义请求头

###### 继承自

PublishConfiguration.requestHeaders

---

##### slug

> readonly slug: string

仓库 slug/名称

---

##### timeout?

> readonly optional timeout: null | number

请求超时（毫秒）。（默认为 2 分钟；0 被忽略）

###### 默认值

```json
120000
```

###### 继承自

PublishConfiguration.timeout

---

##### token?

> readonly optional token: null | string

用于支持从私有 bitbucket 仓库自动更新的应用密码。

---

##### username?

> readonly optional username: null | string

用于支持从私有 bitbucket 仓库自动更新的用户名。

## Github

Electron-Builder / builder-util-runtime / GithubOptions

GitHub 选项。

GitHub 个人访问令牌是必需的。您可以通过访问 https://github.com/settings/tokens/new 生成。访问令牌应具有 repo 范围/权限。
定义 GH_TOKEN 环境变量。

#### 扩展自

- PublishConfiguration

#### 属性

##### channel?

> readonly optional channel: null | string

渠道。

###### 默认值

```json
latest
```

---

##### host?

> readonly optional host: null | string

主机（包括端口，如果需要）。

###### 默认值

```json
github.com
```

---

##### owner?

> readonly optional owner: null | string

所有者。

---

##### private?

> readonly optional private: null | boolean

如果定义了 GH_TOKEN 环境变量，是否使用私有 github 自动更新提供商。请参见私有 GitHub 更新仓库。

---

##### protocol?

> readonly optional protocol: null | "https" | "http"

协议。GitHub 发布商仅支持 https。

###### 默认值

```json
https
```

---

##### provider

> readonly provider: "github"

提供商。必须是 github。

###### 覆盖

PublishConfiguration.provider

---

##### publishAutoUpdate?

> readonly optional publishAutoUpdate: boolean

是否发布自动更新信息文件。

自动更新仅依赖于列表中的第一个提供商（您可以指定多个发布商）。
因此，可能不需要为其他配置的提供商上传元数据文件。但默认情况下会上传。

###### 默认值

```json
true
```

###### 继承自

PublishConfiguration.publishAutoUpdate

---

##### releaseType?

> optional releaseType: null | "draft" | "prerelease" | "release"

发布类型。默认情况下将创建草稿发布。

您也可以使用环境变量设置发布类型。如果 EP_DRAFT 设置为 true —— 草稿，如果 EP_PRE_RELEASE 设置为 true —— 预发布。

###### 默认值

```json
draft
```

---

##### repo?

> readonly optional repo: null | string

仓库名称。自动检测。

---

##### requestHeaders?

> readonly optional requestHeaders: OutgoingHttpHeaders

任何自定义请求头

###### 继承自

PublishConfiguration.requestHeaders

---

##### tagNamePrefix?

> readonly optional tagNamePrefix: string

如果定义，则设置位于语义版本号之前的标签名称前缀。
例如 "v" 在 "v1.2.3" 中或 "test" 在 "test1.2.3" 中。
覆盖 vPrefixedTagName

---

##### timeout?

> readonly optional timeout: null | number

请求超时（毫...