# JustCnc 的 CLIProxyAPI 插件源

这是一个第三方 [CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI) 插件商店
registry，统一维护 JustCnc 开发的插件。它不托管二进制，只维护 `registry.json`；
每个插件的代码、Release 和校验文件仍在各自的 GitHub 仓库里。

Registry URL：

```text
https://raw.githubusercontent.com/JustCnc/cpa_plugins/main/registry.json
```

CLIProxyAPI 内置的官方源始终保留，本仓库只是额外追加的一个源。

## 在 CPA 中使用

在 CPA 的 `config.yaml` 中：

```yaml
plugins:
  store-sources:
    - "https://raw.githubusercontent.com/JustCnc/cpa_plugins/main/registry.json"
```

也可以在 EasyCLIProxyAPI 的 GUI 里打开「配置面板 → 高级 → 第三方插件源」，
每行填一个 URL，保存后重启/重载。之后在「插件商店」里搜索或安装插件即可。

> 只加 `store-sources` 不会改变 `plugins.enabled`；插件系统本身仍需要在 CPA 中启用。

## 当前插件

| id | 名称 | 仓库 | 平台 |
| --- | --- | --- | --- |
| `raccoon2api` | Raccoon2API | [JustCnc/raccoon2api](https://github.com/JustCnc/raccoon2api) | `linux/amd64`、`linux/arm64`、`windows/amd64` |

`raccoon2api` 把商汤小浣熊（`xiaohuanxiong.com`）的 LLM 网关接入 CLIProxyAPI，
对外提供 OpenAI 兼容的 `/v1/chat/completions`、`/v1/models`，并带账号池和内嵌控制台。

## 发版约定

插件商店读取插件仓库的 **最新 GitHub Release**，所以更新插件只需要发布一个新的
Release，不需要修改本仓库的 `registry.json`。Release 必须满足：

- tag 为 `v<version>`，`version` 是点分数字，例如 `v0.1.0`；
- 提供每个支持平台的 zip：

  ```text
  <id>_<version>_<goos>_<goarch>.zip
  ```

  例如：

  ```text
  raccoon2api_0.1.0_linux_amd64.zip
  raccoon2api_0.1.0_linux_arm64.zip
  raccoon2api_0.1.0_windows_amd64.zip
  ```

- 提供一个 sha256sum 格式的 `checksums.txt`：

  ```text
  <sha256>  raccoon2api_0.1.0_linux_amd64.zip
  ```

- 每个 zip 的**根目录**只能放对应平台的动态库，文件名必须是 `<id>.so` /
  `<id>.dll` / `<id>.dylib`。不要嵌套目录，也不要放多个动态库。

`registry.json` 里的 `version` 是可选的展示兜底；正常以最新 Release 的 tag 为准。
`raccoon2api` 的发版由
[`.github/workflows/release.yml`](https://github.com/JustCnc/raccoon2api/blob/main/.github/workflows/release.yml)
自动生成上述 zip 和 `checksums.txt`。

`raccoon2api` 的完整操作步骤（tag、Release、资产校验、registry 更新）见
[RELEASING.md](https://github.com/JustCnc/raccoon2api/blob/main/RELEASING.md)。

## 新增插件

1. 在 `registry.json` 的 `plugins` 数组末尾追加一个对象，至少包含：

   ```json
   {
     "id": "my-plugin",
     "name": "My Plugin",
     "description": "What the plugin does.",
     "author": "JustCnc",
     "repository": "https://github.com/JustCnc/my-plugin"
   }
   ```

   可选项：`version`（不要带前导 `v`）、`logo`、`homepage`、`license`、`tags`。

2. `id` 必须唯一，格式为 `^[A-Za-z0-9][A-Za-z0-9._-]{0,127}$`；
   `repository` 必须是 `https://github.com/{owner}/{repo}`（不要带结尾斜杠）。
3. 确认插件仓库的最新 Release 已经有上面约定好的 `<id>_<version>_<goos>_<goarch>.zip`
   和 `checksums.txt`。
4. 不要在本仓库提交二进制；只提交 `registry.json` 和必要的说明。
5. 提交并推送后，原始 URL 会更新；CPA 刷新插件商店即可看到。

## 说明

- 这是第三方源，不会自动进入官方商店。如果希望进入官方列表，需要另外向
  [router-for-me/CLIProxyAPI-Plugins-Store](https://github.com/router-for-me/CLIProxyAPI-Plugins-Store)
  提交 PR。
- 官方 registry 的格式和校验规则见
  [官方 README](https://github.com/router-for-me/CLIProxyAPI-Plugins-Store#readme)。
