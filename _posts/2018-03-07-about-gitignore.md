---
layout: post
title: "关于.gitignore"
subtitle: "About .gitignore"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - Git
---

## 添加 .gitignore 文件

### 方法 1:新建 repository 时选择添加.gitignore

创建新 repository 页面，在'Create repository'按钮上方，有'Add .gitignore:None'的下拉选项，点击选择需要的.gitignore 模板。

### 方法 2:新建项目之后要添加

可以参考.gitignore 模板: [.gitignore](https://github.com/github/gitignore)，可按需进行改动。

## 如何解决 .gitignore 不起作用

如果一些想要被忽略的文件已经上传到 GitHub 的仓库，或者，当我们 `git add` 之后，突然想起来要添加一个 gitignore 文件，但是一些诸如 node_modules/, cache/ 等文件已经被 add 进去了，这些文件不会被 ignore 掉，怎么办？

```bash
//删除缓存区(Index)的某文件，但是在工作区(working air)保留。-r表示允许递归删除(allow recursive removal)。
git rm -r --cached node_modules
git commit -m 'Remove the now ignored directory "node_modules"'
git push origin master
```

## 创建全局性的.gitignore_global

### Step1: 创建`~/.gitignore_global`文件

```bash
cd ~
touch .gitignore_global
```

打开编辑，写入要全局忽略的文件类型，内容可参考[octocat/.gitignore](https://gist.github.com/octocat/9257657)。

### Step2: 在 `~/.gitconfig`中引入 `.gitignore_global`

```bash
git config --global core.excludesfile ~/.gitignore_global
```

搞定

## 参考资料

1. [Remove directory from remote repository after adding them to .gitignore — stackoverflow](https://stackoverflow.com/questions/7927230/remove-directory-from-remote-repository-after-adding-them-to-gitignore)

2. [让 Git 全局性的忽略 .DS_Store](http://mednoter.com/gitignore-global.html)

3. [Create a global .gitignore — github](https://help.github.com/articles/ignoring-files/#create-a-global-gitignore)
