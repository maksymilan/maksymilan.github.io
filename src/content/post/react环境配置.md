---
title: react环境配置
date: 2024-11-21 17:13:13
updated: "21 Nov 2024"
publishDate: "21 Nov 2024"
description: "这篇文章记录了在WSL中配置React环境的过程，包括安装Node.js、NVM和使用pnpm安装依赖等步骤。"
tags: ["react", "wsl", "nodejs", "nvm", "pnpm"]
---

最近参与一个前端项目，需要配置react环境，记录一下在wsl里面的环境配置过程，主要的命令是参考了微软的wsl中的教程[Install React on Windows Subsystem for Linux | Microsoft Learn](https://learn.microsoft.com/en-us/windows/dev-environment/javascript/react-on-wsl)

# 安装nodejs

安装curl

```bash
sudo apt-get install curl
```

安装nvm

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
```

检查nvm是否安装成功

```bash
command -v nvm
```

检查目前所用的node

```bash
nvm ls
```

安装current和LTS两个版本的Node.js

LTS

```bash
nvm install --lts
```

current

```bash
nvm install node
```

查看当前node

```bash
node --version
```

切换node版本

```bash
nvm use --lts#使用lts版本
nvm use node#使用current
```

react环境安装，由于我这里的项目已经有了配置文件，我只需要进入该文件夹目录下，运行

```bash
pnpm install
```

即可（pnpm安装过程在这里省去）