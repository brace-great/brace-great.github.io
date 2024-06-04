---
title: 使用github Actions引入npm包
date: 2023-05-29 22:47:07
categories: Nodejs
tags:
---

由于我有一个不想上传到 npm 的包，但又需要引用它，所以我考虑直接在 GitHub 上发布该包，并进行引用。下面我将逐步解释如何使用 GitHub Actions，以我的项目为例：

由于使用 TypeScript，所以需要进行编译。在 tsconfig.json 文件中，需要正确配置输出文件夹:

```XML
name: Release

on:
  push:
    branches: ['main']

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.x]
```

监视 main 分支提交

```XML
steps:
  - uses: actions/checkout@v3
  - name: Use Node.js ${{ matrix.node-version }}
    uses: actions/setup-node@v3
    with:
      node-version: ${{ matrix.node-version }}
```

配置 node 环境

```XML
- name: Install pnpm
  uses: pnpm/action-setup@v2
  id: pnpm-install
  with:
    version: 7
    run_install: false
- name: Get pnpm store directory
  id: pnpm-cache
  shell: bash
  run: |
    echo "STORE_PATH=$(pnpm store path)" >> $GITHUB_OUTPUT
- name: Setup pnpm cache
  uses: actions/cache@v3
  with:
    path: ${{ steps.pnpm-cache.outputs.STORE_PATH }}
    key: ${{ runner.os }}-pnpm-store-${{ hashFiles('**/pnpm-lock.yaml') }}
    restore-keys: |
      ${{ runner.os }}-pnpm-store-
- name: Install dependencies
  run: pnpm install
```

由于我使用的是 pnpm，GitHub actions 并不原生支持 pnpm，因此需要安装一下。这是来自 pnpm 自己提供的设置。

```XML
- name: build
  run: pnpm run build
- name: read version
  id: version
  uses: ashley-taylor/read-json-property-action@v1.0
  with:
    path: ./package.json
    property: version
- name: pack
  run: |
    tar -zcvf prism-core.tar.gz ./dist ./template package.json README.md
```

第一步：构建；
第二步：提取 package.json 中的版本号，稍后会用到；
第三步：打包。请注意，npm install 不支持 zip 包，因此需要打包成 tarball 文件。

```XML
- name: Create Release
  id: create_release
  uses: actions/create-release@latest
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  with:
    tag_name: v${{steps.version.outputs.value}}
    release_name: v${{steps.version.outputs.value}}
    draft: false
    prerelease: false
- name: Upload Release Asset
  id: upload-release-asset
  uses: actions/upload-release-asset@v1
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  with:
    upload_url: ${{ steps.create_release.outputs.upload_url }}
    asset_path: ./prism-core.tar.gz
    asset_name: prism-core.tar.gz
    asset_content_type: application/x-tar
```

第一步：根据前面的版本号生成发布版本。
第二步：上传发布版本。请注意，在 actions/general 的 Workflow permissions 中开启项目 actions 的读写权限。

这样，每次提交后都会生成发布版本（记得修改版本号）。

接下来是引入部分。只需使用 npm install 自动发布生成的 tarball 网址即可，例如：npm install [https://github.com/nulla2011/prism-core/releases/download/v0.2.4/prism-core.tar.gz](https://github.com/nulla2011/prism-core/releases/download/v0.2.4/prism-core.tar.gz)。每次提交后，只需修改网址中的版本号，然后运行 npm i 即可。或者，你也可以使用 npm install [https://github.com/nulla2011/prism-core/releases/latest/download/prism-core.tar.gz](https://github.com/nulla2011/prism-core/releases/latest/download/prism-core.tar.gz)，但这种方式没有指定版本号，可能会出现问题，所以需要注意一下。

最后附上完整的 Github Actions 配置：

```XML
name: Release

on:
  push:
    branches: ['main']

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18.x]

    steps:
      - uses: actions/checkout@v3
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
      - name: Install pnpm
        uses: pnpm/action-setup@v2
        id: pnpm-install
        with:
          version: 7
          run_install: false
      - name: Get pnpm store directory
        id: pnpm-cache
        shell: bash
        run: |
          echo "STORE_PATH=$(pnpm store path)" >> $GITHUB_OUTPUT
      - name: Setup pnpm cache
        uses: actions/cache@v3
        with:
          path: ${{ steps.pnpm-cache.outputs.STORE_PATH }}
          key: ${{ runner.os }}-pnpm-store-${{ hashFiles('**/pnpm-lock.yaml') }}
          restore-keys: |
            ${{ runner.os }}-pnpm-store-
      - name: Install dependencies
        run: pnpm install
      - name: build
        run: pnpm run build
      - name: read version
        id: version
        uses: ashley-taylor/read-json-property-action@v1.0
        with:
          path: ./package.json
          property: version
      - name: pack
        run: |
          tar -zcvf prism-core.tar.gz ./dist ./template package.json README.md
      - name: Create Release
        id: create_release
        uses: actions/create-release@latest
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: v${{steps.version.outputs.value}}
          release_name: v${{steps.version.outputs.value}}
          draft: false
          prerelease: false
      - name: Upload Release Asset
        id: upload-release-asset
        uses: actions/upload-release-asset@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          upload_url: ${{ steps.create_release.outputs.upload_url }}
          asset_path: ./prism-core.tar.gz
          asset_name: prism-core.tar.gz
          asset_content_type: application/x-tar
```
