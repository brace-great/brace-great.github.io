---
title: VsCode配置ts开发环境
date: 2021-10-1 22:15:02
tags: ts
categories: Vscode
---

# 打造最舒服的 ts 开发环境

本文将介绍 VS Code 下的 TypeScript 环境搭建：插件以及配置项。对于 TS 文件的执行，我们会介绍 ts-node、ts-node-dev 等工具，帮助你快速验证 TS 代码的执行结果。而如果你只想快速开始学习，我们也会介绍 TypeScript 官方提供的 TypeScript Playground，利用它你可以快速开始编写及分享 TS 代码。最后，我们还会介绍如何通过 TS 声明的方式来检查类型兼容性。

## VS Code 配置与插件

VS Code 本身就是由 TypeScript 编写的，因此它对 TypeScript 有着非常全面的支持，包括类型检查、补全等功能，我们需要的两个 TS 插件都来自于社区，这两个插件分别提供了**类型的自动导入**，和**快速移动 TypeScript 文件**的能力。

首先是  [TypeScript Importer](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Dpmneo.tsimporter) 。这一插件会收集你项目内所有的类型定义，在你敲出:时提供这些类型来进行补全。如果你选择了一个，它还会自动帮你把这个类型导入进来。效果如图所示：

![](http://192.168.196.197:8000/data/img/1662464637444-258e3aa7-3f82-4fed-b275-92677ff70c6e.webp)

这一功能在日常开发中真得非常非常好用，尤其是当项目里有数百个声明分散在各个文件中时。

[Move TS](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3Dstringham.move-ts)，这一插件在重构以及像我们这样写 demo 的场景下很有帮助。它可以让你通过编辑文件的路径，直接修改项目的目录结构。比如从 home/project/learn-interface.ts  修改成  home/project/interface-notes/interface-extend.ts，这个插件会自动帮你把文件目录更改到对应的样子，并且更新其他文件中对这一文件的导入语句。

![](http://192.168.196.197:8000/data/img/1662464637487-584e61df-0fef-4464-aa28-7758df3fbc56.webp)

当然，对于 VS Code 内置的 TypeScript 支持，我们也可以通过一些配置项获得更好的开发体验。首先，你需要通过 Ctrl(Command) + Shift + P 打开命令面板，找到「打开工作区设置」这一项。

![](http://192.168.196.197:8000/data/img/1662464637453-b6249345-640b-47d6-93e8-b11490d479bf.webp)

然后，在打开的设置中输入 typescript，筛选出所有 TypeScript 有关的配置，点击左侧的"TypeScript"，这里才是官方内置的配置。

![](http://192.168.196.197:8000/data/img/1662464637459-89a42b71-771c-4283-bf6e-cc4feeaf81c3.webp)

我们需要做的就是开启一些代码提示功能（hints），我们知道 TS 能够在很多地方进行类型地自动推导，但你往往要把鼠标悬浮在代码上才能看到推导得到的类型，其实我们可以通过配置将这些推导类型显示出来：

在前面配置搜索处，搜索 'typescript Inlay Hints'，展示的配置就都是提示相关的了，推荐开启的有这么几个：

- Function Like Return Types，显示推导得到的函数返回值类型；
- Parameter Names，显示函数入参的名称；
- Parameter Types，显示函数入参的类型；
- Variable Types，显示变量的类型。

当然，并不是所有人都习惯这样的显示方式，你可以根据自己的需要进行调整。除了这些提示的配置以外，VS Code 还支持了百余项 TS 配置，你可以看看是否有你需要的配置。

### 其他插件

除了 TS 强相关的插件与配置，还有一些额外的、能提升你学习效率的插件，你可以依据自己的喜好进行添加，以下的插件列表将会不定期进行更新。

- [ErrorLens](https://link.juejin.cn/?target=https%3A%2F%2Fmarketplace.visualstudio.com%2Fitems%3FitemName%3DPhilHindle.errorlens)，这一插件能够把你的 VS Code 底部问题栏的错误下直接显示到代码文件中的对应位置，比如这样：![](http://192.168.196.197:8000/data/img/1662464638266-b8c69531-4ce7-4025-bc37-1697c0fbf140.webp)

#

## TS 文件的快速执行：ts-node 与 ts-node-dev

当然，如果你主要是想执行 TypeScript 文件，就像  node index.js  这样快速地验证代码逻辑，这个时候你就需要  [ts-node](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FTypeStrong%2Fts-node)  以及  [ts-node-dev](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fwclr%2Fts-node-dev)  这一类工具了。它们能直接执行 ts 文件，并且支持监听文件重新执行。同时，它们也支持跳过类型检查这一步骤来获得更快的执行体验。

对于 ts-node，你可以将其安装到项目本地或直接全局安装，我个人更推荐安装到全局然后配置 alias 快速启动，像  tsn index.ts  这样。执行以下命令将 ts-node 与 typescript 安装到全局：

```
npm i ts-node typescript -g

然后，在项目中执行以下命令创建 TypeScript 的项目配置文件： tsconfig.json。

npx typescript --init // 如果全局安装了 TypeScript，可以这么做 tsc --init

接着，创建一个 TS 文件：

console.**log**("Hello TypeScript");

再使用 ts-node 执行：

ts-node index.ts
```

如果一切正常，此时你的终端能够正确地输出字符。ts-node 可以通过两种方式进行配置，在 tsconfig 中新增  'ts-node'  字段，或在执行 ts-node 时作为命令行的参数，这里我们主要介绍通过命令行进行常用配置的方式。

- -P,--project：指定你的 tsconfig 文件位置。默认情况下 ts-node 会查找项目下的 tsconfig.json 文件，如果你的配置文件是  tsconfig.script.json、tsconfig.base.json  这种，就需要使用这一参数来进行配置了。
- -T, --transpileOnly：禁用掉执行过程中的类型检查过程，这能让你的文件执行速度更快，且不会被类型报错卡住。这一选项的实质是使用了 TypeScript Compiler API 中的 transpileModule 方法，我们会在后面的章节详细讲解。
- --swc：在 transpileOnly 的基础上，还会使用 swc 来进行文件的编译，进一步提升执行速度。
- --emit：如果你不仅是想要执行，还想顺便查看下产物，可以使用这一选项来把编译产物输出到  .ts-node  文件夹下（需要同时与  --compilerHost  选项一同使用）。

除了直接使用 ts-node 以外，你也可以通过 node + require hook 的形式来执行 TS 文件：

```
node -r ts-node/register index.ts
```

但此时，如果想要传递参数给 ts-node ，你就需要使用环境变量了，比如要传递之前的 transpileOnly 选项：

TS_NODE_TRANSPILE_ONLY=true node -r ts-node/register index.ts

关于选项对应的环境变量，请参考 ts-node 的官方文档了解更多。

ts-node 本身并不支持自动地监听文件变更然后重新执行，而这一能力又是某些项目场景下的刚需，如 NodeJs API 的开发。因此，我们需要  [ts-node-dev](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fwclr%2Fts-node-dev)  库来实现这一能力。ts-node-dev 基于  [node-dev](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Ffgnass%2Fnode-dev)（你可以理解一个类似 nodemon 的库，提供监听文件重新执行的能力） 与  [ts-node](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FTypeStrong%2Fts-node)  实现，并在重启文件进程时共享同一个 TS 编译进程，避免了每次重启时需要重新实例化编译进程等操作。

首先，我们还是在全局安装：

```
npm i ts-node-dev -g

ts-node-dev 在全局提供了 tsnd 这一简写，你可以运行 tsnd 来检查安装情况。最常见的使用命令是这样的：

ts-node-dev --respawn --transpile-only app.ts
```

respawn 选项启用了监听重启的能力，而 transpileOnly 提供了更快的编译速度。你可以查看官方仓库来了解更多选项，但在大部分场景中以上这个命令已经足够了。

## 更方便的类型兼容性检查

某些时候，我们在进行类型比较时，需要使用一个具有具体类型的变量与一个类型进行赋值操作，比如下面这个例子中：

```
interface Foo {
  name: string;
  age: number;
}
interface Bar {
  name: string;
  job: string;
}
let foo:Foo = {
  name: '林不渡',
  age: 18
}
let bar:Bar = {
  name: '林不渡',
  job: 'fe'
}
foo = bar;
```

在“只是想要进行类型比较”的前提下，其实并没有必要真的去声明两个变量，即涉及了值空间的操作。我们完全可以只在类型空间中（你可以理解为**用于存放 TypeScript 类型信息的内存空间**）比较这些类型，只需要使用 declare 关键字：

```
interface Foo {
  name: string;
  age: number;
}
interface Bar {
  name: string;
  job: string;
}
declare let foo: Foo;
declare let bar: Bar;
foo = bar;
```

你可以理解为在开始时的例子，我们使用一个值空间存放这个变量具体的属性，一个类型空间存放这个变量的类型。而通过 declare 关键字，我们声明了一个仅在类型空间存在的变量，它在运行时完全不存在，这样就避免了略显繁琐的属性声明。

对于类型兼容的检查，除了两两声明然后进行赋值以外，我们还可以通过工具类型的形式，如  [tsd](https://link.juejin.cn/?target=https%3A%2F%2Fwww.npmjs.com%2Fpackage%2Ftsd)  这个 npm 包提供的一系列工具类型，能帮助你进行声明式的类型检查：：

```
import { expectType } from 'tsd';
expectType<string>("linbudu"); // √
expectType<string>(599); // ×
```

这一部分的内容并不是初学需要掌握的，但你可以选择提前用起来，不必急着去理解具体的实现原理。

它的结构大致是这样：expectType<你预期的类型>(表达式或变量等)，除了  expectType（检查预期类型与表达式或变量的类型是否一致），tsd 还提供了  expectNotType（检查预期类型与表达式或变量的类型是否不同）、expectAssignable（检查表达式或变量的类型是否能赋值给预期类型）等工具类型，其中涉及工具类型与泛型的知识，我们会在后面的课程中一一讲解。

## 总结

我们了解了 TypeScript 开发环境的搭建，包括了 VS Code 的配置、插件，使用 Playground 作为一个简易又强大的临时编辑器，以及如何使用 ts-node 与 ts-node-dev 来快速执行你的 ts 文件。在最后，我们稍微提前了一些对后面学习大有裨益的知识，即通过类型声明（declare）与 tsd 来进行更方便的类型兼容性检查。
