---
id: git-server
title: "Git 在服务器上的使用"
description: "讲解各种我在服务器上使用 git 的技巧“
publishDate: "2025-06-020T00:00:00Z"
---

# 如何为不同的项目使用不同的 Github 账户认证

在服务器上使用git的时候，全局的git可能不是自己的账户，那么如何让自己在服务器上的项目能commit到自己的github上呢，我在课题组的服务器上干活的时候就遇到了这个问题。

## 1. 配置项目本地的 git config
在项目目录下执行以下命令：

```bash
git config user.name "Your Name"
git config user.email " 
```
这两条命令能在项目本地覆盖全局的git配置。
可以使用
```bash
git config --list --show-origin
```
来查看当前项目的git配置。

## 2. 配置 SSH key
通过ssh连接 github 账户，需要为自己的账户生成一个 SSH key，并将其添加到 GitHub 账户中。可以使用以下命令生成 SSH key：

```bash
ssh-keygen -t id_ed25519 -C "Your Email" -f ~/.ssh/id_ed25519_yourname
```
生成的 SSH key 会保存在 `~/.ssh/id_ed25519_yourname` 和 `~/.ssh/id_ed25519_yourname.pub` 中。将公钥（`.pub` 文件）添加到 GitHub 账户的 SSH keys 中。

## 3. 配置 SSH config
然后配置对应的 SSH config 文件，以便在连接到 GitHub 时使用正确的 SSH key。编辑 `~/.ssh/config` 文件，添加以下内容：

```plaintext
Host ProjectName<可以任意自定义>
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_yourname
    IdentitiesOnly yes
```

## 4. 修改远程仓库地址
最后，修改项目的远程仓库地址，以便使用新的 SSH 配置。可以使用以下命令：
```bash
git remote set-url origin git@ProjectName:username/repo.git
```
将 `ProjectName` 替换为你在 SSH config 中定义的主机名，`username/repo.git` 替换为你的 GitHub 用户名和仓库名

## 5. 测试连接
可以使用以下命令测试 SSH 连接是否成功：
```bash
ssh -T git@ProjectName
```
如果配置正确，你应该会看到类似以下的消息：
```plaintext
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```

