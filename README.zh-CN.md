# JekyllNet GitHub Action（中文说明）

[English](README.md) | [简体中文](README.zh-CN.md)

此仓库提供独立的 GitHub Action，用于通过 `JekyllNet` 构建 Jekyll 风格的静态站点。

该 Action 的执行流程如下：

1. 安装 `.NET 10`
2. 从 NuGet 安装 `JekyllNet` dotnet tool
3. 执行 `jekyllnet build`
4. 可选：把构建产物上传为 workflow artifact

## 使用示例

```yml
name: build-site

on:
  push:
  pull_request:
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v5

      - name: Build docs with JekyllNet
        uses: JekyllNet/action@v2.5
        with:
          source: ./docs
          destination: ./artifacts/docs-site
          verbose: "true"
          upload-artifact: "true"
          artifact-name: docs-site
```

如果你希望从 GitHub Packages（而不是 NuGet.org）安装，请传入可读取包的令牌：

```yml
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v5

      - name: Build docs with JekyllNet from GitHub Packages
        uses: JekyllNet/action@v2.5
        with:
          source: ./docs
          nuget-source: https://nuget.pkg.github.com/JekyllNet/index.json
          nuget-auth-token: ${{ secrets.JEKYLLNET_PACKAGES_TOKEN }}
```

## 输入参数

- `source`
- `destination`
- `drafts`
- `future`
- `unpublished`
- `posts-per-page`
- `tool-version`
- `nuget-source`
- `nuget-auth-token`
- `nuget-username`
- `dotnet-version`
- `setup-dotnet`
- `cache`
- `verbose`
- `upload-artifact`
- `artifact-name`

## 说明

- 默认 .NET SDK 为 `10.0.x`。
- 默认 NuGet 源为 `https://api.nuget.org/v3/index.json`。
- 默认 `tool-version` 为 `0.2.5`，因此 `JekyllNet/action@v2.5` 会安装匹配的 CLI 版本，除非你显式覆盖。
- 默认开启缓存，会在 workflow 之间缓存 NuGet 包和已安装的全局工具。
- 如果 `tool-version` 固定且命中缓存，会跳过重复安装。
- 如果 `tool-version` 为空，Action 仍会检查最新版本，但会先恢复缓存以减少重复下载。
- `verbose` 默认值为 `true`。当已安装的 JekyllNet 支持 `--verbose` 时，Action 会自动启用更详细的逐文件构建日志。
- 如果安装的 JekyllNet 版本较旧、不支持 `--verbose`，Action 会记录提示并继续执行，不会让 workflow 失败。
- 每次构建后，Action 会输出目标目录、生成文件数量和简短样例文件列表，便于在日志中快速确认产物已生成。
- `nuget-auth-token` 与 `nuget-username` 仅在需要认证的源（例如 GitHub Packages）中才需要。
- 当 `nuget-source` 指向 GitHub Packages 且 `nuget-auth-token` 为空时，Action 会回退使用 `github.token`。
- 跨仓库读取 GitHub Packages 时，通常建议使用具备包读取权限的 PAT 或 fine-grained token，而不是默认 workflow token。
- 该 Action 假定 `JekyllNet` dotnet tool 已经发布到你配置的 NuGet 源。
