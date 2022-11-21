---
title: Windows开启webdav
date: 2021-2-12 21:07:22
tags: webdav
categories: Windows
---

## 先决条件：

• 由 IIS 安装创建的默认网站必须仍然存在。
• 必须安装 Internet Information Services 管理器。
• 必须安装 Windows 身份验证。
• 必须安装 WebDAV 重定向程序。

## 操作步骤

### 服务端

打开"添加角色和功能"
点击下一步直到服务器角色选择界面
在服务器角色界面勾选"Web 服务器(IIS)"
在功能界面勾选"WebDAV 重定向程序"
在角色服务界面勾选"Windows 身份验证、WebDAV 发布"
在确认界面勾选"如果需要，自动重新启动目标服务器"，弹窗点击"是"，然后点击"安装"
功能安装中，安装完成后会自动重启服务器
重启完成后自动弹出安装进度，等到安装完成后关闭窗口 WebDAV 服务配置
先创建用于 WebDAV 共享的文件
打开"Internet Information Services (IIS)管理器"
在"Default Web Site"上右击"添加虚拟目录"
填写别名为"WebDAV"，物理路径为"C:\WebDAV"
左侧选择"WebDAV"，打开"WebDAV 创作规则"
右击"添加制作规则"
添加制作规则，选择所有用户，给予读取、源、写入权限
左侧选择"WebDAV"，打开"身份验证"
启用"Windows 身份验证"，禁用"匿名身份验证"

### 客户端

打开"映射网络驱动器"
填写 WebDAV 地址"http://localhost/WebDAV"
连接成功后会弹出 WebDAV 的文件，到此为止可以愉快的使用 WebDAV

- 补充一下，IIS 里 webdav 的 目录浏览 启用 IIS 里还要把上面一级目录 Default Web Site 的 WebDAV 启用，否则只能在浏览器 http 访问，添加映射网络驱动器会提示 windows 无法访问 http://xxx.xxx.xxx.xxx 还有文件夹权限!!!
