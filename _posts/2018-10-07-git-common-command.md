---
layout: post
title: "常用的 Git 命令"
subtitle: "Common command of git"
author: "Rushan"
header-img: "img/post-bg-git.png"
header-mask: 0.4
tags:
  - Git
---

Git的四个区：

- 工作区
- 暂存区
- 本地仓库
- 远程仓库（自己的远程仓库，公司的远程仓库）

## 新建/拷贝项目

如果是从零开始，新建项目，初始化：

```bash
git init [project-name]
```

如果是开发已有项目，先fork公司的项目作为自己的远程仓库，再拷贝自己的远程仓库到本地：

```bash
git clone [url]
```

## 关联远程仓库

关联本地与(自己的、公司的)远程仓库：

```bash
git remote add [shortname] [url]
```

- `shortname`为远程仓库指定一个简单的名字，自己的远程仓库一般称为`origin`，公司的可以称为`work`
- `url` （自己的/公司的）远程仓库链接。

查看已关联的远程仓库：

```bash
git remote -v
```

## 修改项目

在工作区修改文件后，查看文件状态：

```bash
git status
```

追踪文件，添加到暂存区：

```bash
// 工作区 => 暂存区
git add .
git add [file-name]
```

提交本次修改的信息到本地仓库

```bash
// 暂存区 => 本地仓库
git commit -m "message"
```

查看commit历史：

```bash
git log
```

## 关于分支

公司中，一般有3个基本分支：`dev`分支（用于开发测试），`test`分支（用于测试），`master`分支（线上代码）。

接到一个需求，先从`master`切出一个独立分支：

```bash
git checkout -b [feature-branch-name]
```

修改项目，见👆。

完成需求开发后，把独立分支合并到`dev`分支上：

```bash
// 切换分支
git checkout dev
// 合并某分支到当前分支
git merge [feature-branch-name]
```

接着拉取更新公司远程的`dev`分支代码：

```bash
// 远程仓库 => 工作区
git pull work dev
```

拉取后，如有冲突，解决冲突，然后`push`到自己的远程仓库：

```bash
// 本地仓库 => 远程仓库
git push origin dev
```

然后向公司的远程仓库提Pull Request，老大审完代码，没问题就可以合并到公司的`dev`分支。

等需求上线后（merge到公司的master分支），就可以删除本地及自己远程仓库里这个需求的分支：

```bash
// 删除本地分支
git branch -D [feature-branch-name]
// 删除远程分支
git push origin --delete [feature-branch-name]
```

## 同步远程更新

### 获取远程新分支

有时公司仓库新增了分支，用`git branch -a`查看所有分支也查看不到，需要`fetch`一下，把新分支下载到本地（下载的内容与自己的本地仓库互不干扰）：

```bash
// 远程仓库新分支 => 本地
git fetch work [new-branch]
```

这时用`git branch -a`就能看到新分支了。但是新分支还不在工作区。

在远程新分支`work/[new-branch]`的基础上，在工作区创建一个新分支：

```bash
git checkout -b [workspace-new-branch] work/[new-branch]
```

新分支要推送自己的远程仓库的话，切换到新分支，关联自己的远程仓库。

### 获取远程已有分支的更新

如果公司的仓库没有新增新分支，而是在原有分支上有了更新。同样，先`fetch`到本地（以获取`master`分支的更新为例）：

```bash
// 远程仓库master => 本地
git fetch work/master
```

查看自己的仓库与公司的仓库有哪些不同：

```bash
git diff master work/master
```

终端会进入`Vim`环境，按一下 `q`键，退出`Vim`环境。

对比完，把内容合并到当前分支：

```bash
// 合并work仓库的master分支
git checkout master
git merge work/master
```