---
title: "Barrier 连接 windows 和 mac"
description: "使用 Barrier 连接 Windows 和 macOS 设备，跨平台共享鼠标和键盘。"
publishDate: "1 Jul 2025"
updatedDate: "1 Jul 2025"
tags: ["Barrier", "cross-platform", "mouse", "keyboard", "Windows", "macOS"]
---

# 使用 Barrier 连接 Windows 和 macOS
Barrier 是一个开源的跨平台软件，允许用户在多台计算机之间共享鼠标和键盘。它是 Synergy 的一个分支，提供了类似的功能，但完全免费且开源。以下是如何在 Windows 和 macOS 上设置 Barrier 的步骤。

## 安装 Barrier

### 在 Windows 上安装 Barrier

1. 访问 [Barrier 的 GitHub 页面](https://github.com/debauchee/barrier/releases)。
2. 下载最新版本的 Windows 安装程序（.exe 文件）。
3. 运行安装程序并按照提示完成安装。

### 在 macOS 上安装 Barrier

1. 访问 [Barrier 的 GitHub 页面](https://github.com/debauchee/barrier/releases)。
2. 下载最新版本的 macOS 安装程序（.dmg 文件）。
3. 打开下载的 .dmg 文件，并将 Barrier 拖动到应用程序文件夹中。

## 配置 Barrier
### 在 Windows 上配置 Barrier
1. 打开 Barrier 应用程序。
2. 选择客户端（作为被控制的电脑）

### 在 macOS 上配置 Barrier
1. 打开 Barrier 应用程序。
2. 选择服务器（作为控制电脑）
3. 进入 /Users/hudou/Library/Application Support/barrier/SSL（如果没有的话就自己创建）
4. 输入指令
   ```bash
   openssl req -x509 -newkey rsa:2048 -keyout Barrier.key -out Barrier.crt -days 365 -nodes
   ```
   这将生成一个新的 SSL 密钥和证书文件。
   然后将私钥（.key）内容添加到公钥内容（.pem）
5. 选择连接的屏幕，注意客户端的名称和服务端连接的名称一定要一样
6. 点击“Start”按钮启动 Barrier 服务。就会自动连接上了
