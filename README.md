# JekyllNet GitHub Action

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
      - uses: actions/checkout@v4

      - name: Build docs with JekyllNet
        uses: JekyllNet/action@main
        with:
          source: ./docs
          destination: ./artifacts/docs-site
          upload-artifact: "true"
          artifact-name: docs-site
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
- `dotnet-version`
- `setup-dotnet`
- `upload-artifact`
- `artifact-name`

## Notes

- The default .NET SDK is `10.0.x`.
- The default NuGet source is `https://api.nuget.org/v3/index.json`.
- This action assumes the `JekyllNet` dotnet tool has already been published to the configured NuGet feed.
