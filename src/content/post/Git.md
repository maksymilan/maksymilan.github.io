---
title: Git
date: 2024-08-16 12:34:21
updated: "16 Aug 2024"
publishDate: "16 Aug 2024"
description: "这篇文章介绍了Git的基本操作，包括初始化代码库、提交变更、分支管理等内容，帮助读者快速上手Git。"
tags: ["Git", "version control", "programming"]
---

# 初始化git代码库

主要有以下两种方式

- 从本地创建代码库

  `git init`，执行这个命令后能在目标文件夹下得到一个`.git`后缀的子目录，创建了一个新的主分支

  `git init <project directory>`将在当前文件夹下寻找`<project directory>`文件，如果没有找到就会自动生成一个该名称的文件夹，并在该子文件夹内生成一个`.git`后缀的子目录

- 从远程克隆代码库

  `git clone <repo url>`  ,clone指令支持http协议和git ssh协议，git ssh协议的格式为`git@HOSTNAME:USERNAME/REPONAME.git`

# 将变更保存到代码库

已经有了代码库之后，我们对代码库中的文件进行修改后要进行版本变更。我们的操作有

1. cd到目标的仓库路径下
2. 使用`git add`将修改的文件添加到代码库暂存区域（待提交状态）
3. 使用`git commit`添加相关的修改信息完成提交

`git add --all`能将所有更改的和为跟踪的文件放入代码库暂存区域

# 库间协作

git是分布式的版本控制系统，每一个代码仓库都是完整的代码库，git的代码库之间通过推送（push）和拉取（pull）。如果指定某一个git仓库为中心库，那么我们就可以实现中心式版本控制系统。

通过`git clone`创建的本地仓库已经实现了远程连接，指向的是克隆的代码库地址，`git push`会将本地提交的变更推送到该远程仓库。而`git init`创建的新的代码库，没有远程代码库来接收推送的变更。

# 配置和设置

我们用`git init`创建的代码库可以连接到已有的远程仓库进行代码库的互动。设置好远程仓库后，我们通过`git remote add <name> <remote_repo_url>`连接远程仓库，`<name>`是我们对连接的该远程仓库的命名，因为一个本地仓库可以连接多个远程仓库，用于区分，`<remote_repo_url>`是我们所连接的远程仓库的地址。连接好远程仓库后，我们通过`git push -u <name> <local_branch_name>`将本地分支推送到远程代码仓库

`git remote`：列出与其他储存库的远程连接

`git remote rm <name>`：移除与名为`<name>`的仓库连接

`git remote rename <old-name> <new-name>`：重命名

`<local_branch_name>`可以通过`git branch`查看本地分支，然后选择自己更改的分支

`git config --global user.name <name>`：定义当前储存库中所有提交的作者姓名

`git confit --local user.name <name>` ：定义当前用户所有提交的姓名

`git config --alias.ci commit`：定义一个`ci`命令，作为`git commit`的快捷方式

# 提交

`git add`：暂存变更

`git reset`：撤销提交或暂存的变更

`git status`：检查储存库的当前状态

`git diff <filename>`：查看文件的修改内容

# 版本控制

`git log`：查看提交日志，日志是由时间从近到远排列的

Git当中，`HEAD`表示当前版本,`HEAD^`表示上一个版本，`HEAD~n`表示上n个版本的简写。我们用`git rest`进行版本的回退

`git reset --hard HEAD^`表示回退到上一个版本，`--hard`表示回到上个版本的已提交状态，`--soft`回退到未提交状态，`--mixed`回退到已添加但为提交状态。

如果想回到回退之前的状态，我们用`git reset --hard <commit id>`，其中`<commit id>`不必写全，只需要前几位就可以，git会自动寻找。

git的不同版本的切换实际上只是改变了内部的指向当前版本的`HEAD`指针

通过`git reflog`我们可以找到每一次命令的记录，从而寻找到之前的版本的`<commit id>`

# 分支管理

创建一个分支，然后自己在该分支上工作，开发完毕后，再合并到原来的分支上，这样既可以保证代码不会丢失，也不会因为自己没有写完整代码而影响其他人的工作。

创建一个分支，实际上是创建了一个新的指针，这个指针指向目前的位置，然后将HEAD指向这个指针上，再进行修改时，原来的master分支不动，只对新建的指针进行移动，这个过程是分支的创建和在分支上进行工作。当我们完成在分支上的工作后，要回到master分支，我们需要将现有的分支与master分支进行合并，合并的方法就是将master分支指向当前的分支，然后删除该分支，只剩下master分支。

`git checkout -b dev`：新建一个分支并跳转到这个分支，为了避免和`git checkout -- <file>`这一撤销修改的命令产生混淆，我们用`git switch -c dev`等价替代。

`git branch -d <name>`：删除分支

`git merge <name>`：合并某分支到当前分支

**分支中产生的冲突情况**

如果在dev分支中修改的文件没有及时和master分支进行合并，同时在master分支中还对这些文件进行了修改，那么在进行合并的时候就会产生冲突，只有手动解决冲突之后才能进行commit，

`git log --graph`命令可以看到分支合并图

**分支管理策略**

使用--no-ff方式的`git merge`能在merge时生成一个新的commit

**bug修复**

我们通过新的临时分支来修复bug，修复后，合并分支，并删除临时分支。

`git stash`能将当前分支的工作储存起来，避免了在其他分支上建立临时分支修改bug的时候，当前分支的工作丢失，修复工作完成后，要恢复原先的工作区，`git stash list`能显示储存的内容，`git stash apply@{index}`恢复下标为`index`的工作，并且该工作在`stash list`当中没有被删除，还要使用`git stash drop`，或者直接使用`git stash pop`。

如果我们想将bug的修改应用到当前的工作分支的话，我们可以利用`git cherry-pick <commit id>`来将某一个commit内容直接应用到当前工作分支。

**添加feature**

开发一个新功能，最好新建一个分支，如果要丢弃一个未被合并过的分支，可以通过`git branch -D <name>`强行删除

**rebase**

rebase和merge的作用相似，但不同的点在于merge是基于分支的公共祖先和新提交的内容形成一个新的节点，原来的分支信息不变，而rebase是将多条分支合并为一条分支，使历史版本记录更整洁

`git log graph` ：显示分支的图状结构

`git rebase`：对分支进行变基

rebase也能实现线性结构中多个commit的合并

1. `git rebase -i HEAD~n`：从当前位置开始向前的n个commit进行合并
2. 第一步的指令之后，会进入vim编辑器内，将除了第一个的所有commit的信息更改为`fixup`

这样就实现了多个commit的合并

# 标签管理

标签就是某一个commit的别名，方便我们进行版本的查找（避免通过哈希值进行查找）

**创建标签**

首先切换到需要标签的分支，输入`git tag <name>`，标签默认是打在最新提交的commit，如果要对历史版本上的commit进行打标签，那么需要找到它的commit id，输入`git tag <name> <commit id>`。标签是按字典序进行排序的，而不是创建的时间顺序。

`git show <tagname>`可以查看标签信息

`git tag -a v0.1 -m "...." <commit id>`中`-a`指定标签名，`-m`指定说明文字。

`git tag -d <tagname>`删除标签

标签都只储存到本地，如果要推送某个标签到远程，使用`git push <remote-name> <tagname>`

或者一次性推送所有标签`git push <remote-name> --tags`

如果标签已经推送到远程，这个时候进行删除要先从本地删除，然后从远程删除

`git tag -d <tagname>` + `git push <remote-name> :refs/tags/<tagname>`

# 使用git的过程中遇到的一些问题

## 如何将自己fork的项目与源项目进行同步

1. 将源项目添加为远程仓库

   ```git
   git remote add <name> <url>
   ```

2. 然后将源项目的分支拉到本地

   ```git
   git fetch <name>
   ```

3. 使用`git branch -r`检查项目分支，然后将源项目的分支合并到当前分支，再解决合并冲突，以源项目的分支为`/upstream/main`为例

   ```bahs
   git merge upstream/origin
   ```
   
4. 添加忽略文件

   ```git
   touch .gitignore//创建ignore文件
   ```

   在 `.gitignore` 文件中，添加需要忽略的文件或目录的规则。例如：

   ```
   # 忽略编译生成的文件
   *.o
   *.out
   *.exe
   
   # 忽略 C++ 构建目录
   /build/
   
   # 忽略临时文件
   *.tmp
   *.log
   *.bak
   
   # 忽略操作系统生成的隐藏文件
   .DS_Store
   Thumbs.db
   
   # 忽略 IDE 配置文件
   .vscode/
   *.swp
   ```

   然后将`.gitignore`文件添加到git中

   ```git
   git add .gitignore
   git commit - m "添加ignore文件"
   ```

5. 清理`git add`

   ```git
   git reset
   ```

   ```
   git reset <file>//清理指定的文件
   ```

   

