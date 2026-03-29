# JekyllNet GitHub Action

[English](README.md) | [简体中文](README.zh-CN.md)

This repository hosts the standalone GitHub Action for building Jekyll-style static sites with `JekyllNet`.

The action flow is:

1. install `.NET 10`
2. install the `JekyllNet` dotnet tool from NuGet
3. run `jekyllnet build`
4. optionally upload the generated site as a workflow artifact

## Usage

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

If you intentionally install from GitHub Packages instead of NuGet.org, pass a token that can read the package:

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

## Inputs

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

## Notes

- The default .NET SDK is `10.0.x`.
- The default NuGet source is `https://api.nuget.org/v3/index.json`.
- The default `tool-version` is `0.2.5`, so `JekyllNet/action@v2.5` installs the matching CLI release unless you override it explicitly.
- The cache is enabled by default and stores NuGet packages plus the installed global tool between workflow runs.
- If `tool-version` is pinned, an exact cache hit skips tool installation entirely.
- If `tool-version` is empty, the action still checks for the latest version, but it restores package caches first to reduce repeated downloads.
- `verbose` defaults to `true`. When the installed JekyllNet tool supports `--verbose`, the action enables richer per-file build logging automatically.
- If the installed JekyllNet tool is older and does not support `--verbose`, the action logs a notice and continues without failing the workflow.
- After each build, the action prints the destination path, generated file count, and a short sample file list so workflow logs make it obvious that output was produced.
- `nuget-auth-token` and `nuget-username` are only needed for authenticated feeds such as GitHub Packages.
- When `nuget-source` points to GitHub Packages and `nuget-auth-token` is empty, the action falls back to `github.token`.
- For cross-repository GitHub Packages installs, a PAT or fine-grained token with package read access is usually safer than the default workflow token.
- This action assumes the `JekyllNet` dotnet tool has already been published to the configured NuGet feed.
