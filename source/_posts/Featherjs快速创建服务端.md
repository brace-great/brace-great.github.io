---
title: Featherjs快速创建服务端
date: 2021-5-1 21:59:33
tags: Featherjs
categories: Nodejs
---

## 安装构建工具

```
npm install @feathersjs/cli -g
```

## 创建一个 Feather 应用

```
mkdir feathers-chat  // 创建一个目录  feathers-chat是文件夹名称你可以修改
cd feathers-chat/   // 进入到这个目录
feathers generate app  // 创建应用
```

## 修改配置

config 目录下修改 default.json 修改正确的 mysql 的链接信息 修改为你真是的 mysql 的链接地址
如果你的数据库是有密码的还需要去设置一个 密码
在 src 目录下的 sequelize.ts 文件进行设置

## 新建服务

```
feathers generate service
```

## 关于字段的描述

```
{
  name:{
      type:  数据类型参考 后面的链接,
      allowNull: 布尔值 是否允许为空,
      defaultValue: 默认值,
      primaryKey: 是否是组件,
      autoIncrement: 是否自增,
      unique: 是否唯一,
      comment: 备注信息,

  }
}
```

可以参考
https://www.sequelize.cn/core-concepts/model-basics#%E5%88%97%E5%8F%82%E6%95%B0
