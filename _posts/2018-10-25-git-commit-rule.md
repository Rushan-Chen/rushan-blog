---
layout: post
title: "Git commit message 规范"
subtitle: "Git commit message rule"
author: "Rushan"
header-img: "img/post-bg-git.png"
header-mask: 0.4
tags:
  - Git
---

commit message 有多种规范，本文介绍的是[Angular 规范](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.greljkmo14y0)。

每次提交，Commit message 都包括三个部分：Header，Body 和 Footer。

## Header

Header 是必需的，Body 和 Footer 可以省略。

Header 部分只有一行，包括三个字段：`type`（必需）、`scope`（可选）和`subject`（必需）。

### type

- feat：新功能（feature）
- fix：修补 bug
- docs：文档（documentation）
- style： 格式（不影响代码运行的变动）
- refactor：重构（即不是新增功能，也不是修改 bug 的代码变动）
- test：增加测试
- chore：构建过程或辅助工具的变动

### scope

`scope`用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

### subject

`subject`是 commit 目的的简短描述，不超过 50 个字符。

- 以动词开头，使用第一人称现在时，比如 change，而不是 changed 或 changes
- 第一个字母小写
- 结尾不加句号（.）

## Revert

有一种特殊情况，如果当前 commit 用于撤销以前的 commit，则必须以`revert:`开头，后面跟着被撤销 Commit 的 `Header`。

```bash
revert: feat(pencil): add 'graphiteWidth' option

This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
```

## gitmoji

在 message 开头加上 emoji 表情，用 [gitmoji](https://gitmoji.carloscuesta.me/)。

比如：

- `:sparkles:feat:`
- `:bug:fix:`
- `:memo:docs:`
- `:art:style:` (`:lipstick:style:`)
- `:recycle:refactor:`
- `:white_check_mark:test:`
- `:construction_worker:chore:`

### Terminal Emojify

如果想在本地终端能看到 emoji 图形，那就使用[Terminal Emojify](https://github.com/as-cii/terminal-emojify)。

注意：Terminal Emojify 依赖于 Ruby 环境，就像 Express.js 依赖于 Node.js 环境一样。所以在安装这个库之前需要先配置好 Ruby 环境。首先安装 [Ruby](https://www.ruby-lang.org/en/downloads/)，然后安装 [RubyGems](https://rubygems.org/pages/download)。

然后用`gem` 安装 Terminal Emojify

```bash
gem install terminal-emojify
```

(用 vim、nano 等终端编辑器)编辑 `.gitconfig` 文件，增加下面的内容，美化 `git log` 命令的输出结果。

```bash
[alias]
  hist = !git --no-pager log --color --pretty=format:'%C(yellow)%h%C(reset)%C(bold red)%d%C(reset) %s %C(black)— %an (%ad)%C(reset)' --relative-date | emojify | less --RAW-CONTROL-CHARS
```

如果已经有 commit 用到 emoji 表情，在终端执行`git hist`查看即可看到 emoji 图形，按 Q 键退出查看。

![git hist](https://ws4.sinaimg.cn/large/006tNbRwgy1fwvta2crk9j30vi02mjry.jpg)

如果出现怪异的图形，那可能是没有安装`less`，Mac OS 可以用[Homebrew](http://brew.sh/)安装：

```bash
brew install less
```

## 参考资料

- [Commit message 和 Change log 编写指南 — 阮一峰](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
- [Git Commit Message 规范及 Gitmoji 使用指南 — 岁月留痕](https://hewei.in/git/git-commit-convention-and-gitmoji.html#git-commit-message-%E8%A7%84%E8%8C%83%E5%8F%8A-gitmoji-%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97)
- [gitmoji](https://gitmoji.carloscuesta.me/)
- [Terminal-emojify — Github](https://github.com/as-cii/terminal-emojify)
