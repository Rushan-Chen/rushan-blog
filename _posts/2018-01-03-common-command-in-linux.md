---
layout: post
title: '常用命令'
subtitle: 'About common command in linux'
author: 'Rushan'
header-img: 'img/post-bg-universe.jpg'
header-img-credit: ""
header-img-credit-href: ""
header-mask: 0.4
tags:
  - Linux
---

本篇列举了一些基本的文件操作命令，刚开始如果不熟悉，可以用“man 魔法”查看使用手册，也就是在终端执行 `man command`，其中 command 换成想查的命令。例如`man cp`。

## 目录相关操作

操作目录之前，先看看几个特殊目录：

```bash
.   代表当前目录
..  代表上一层目录
-   代表前一个工作目录（上一次访问的目录）
~   代表目前用户身份所在的主文件夹
```

每个目录下都有`.` 和 `..` 目录，根目录下的 `.` 和 `..` 都代表当前目录。

### 处理目录的命令

- `cd` 切换目录（change directory）
- `pwd` 显示当前目录（print working directory）
- `mkdir` 新建一个新目录（make directory）
  - `mkdir -p` 递归创建目录
  - `mkdir -m` (mode) 配置文件的权限
  - `mkdir -v` (verbose) 新建目录后会提示新建了哪些目录
- `rmdir` 删除一个空文件夹（remove directory）
  - `rmdir -p` 递归删除空目录

```bash
cd /tmp <== /tmp目录为(Mac)系统临时目录，系统会定期的自动清空该文件夹。可在该目录练习

mkdir adir bdir <== 可以创建一个或多个目录
mkdir -v adir bdir <== 会提示 mkdir: created directory 'adir' /n mkdir: created directory 'bdir'

mkdir test1/test2/test3/test4 <== 没法直接递归创建目录
mkdir -p test1/test2/test3/test4 <== 成功创建目录
```

```bash
cd /tmp
ls <== 查看有哪些目录

rmdir adir <== 空目录，可直接删除
rmdir test1 <== 非空目录，无法删除
rmdir -p test1/test2/test3/test4 <== 可以删除，从右往左删除空目录

ls <== 查看目录列表，看看前面删除的目录是不是不在了
```

如果要删除的目录里有其他内容呢？用`rm -r test1`，但是，慎用 `rm -r`！递归删除是很危险的，至少加`-i` 来提示是否确认删除 `rm -ir teset1`。

## 查看文件和目录

`ls` 列出目录内容（list directory contents）

这个常用命令有很多可带的参数，这里看几个常用的

```bash
-a  全部文件，包括开头为`.`的隐藏文件
-l  列出长信息，包括文件属性、权限、容量、最后修改时间等信息
-F  给文件、目录附加数据结构标识，比如，`*`表示可执行文件，`/`表示目录，`=`表示 socket 文件
-h  将文件容量以易读的方式列出来（B，K等），需要与`-l`合用，即`-lh`
-R  连同子目录内容都列出来
```

`ls -l` 显示的长串信息：

```bash
drwxr-xr-x   2 chenrushan  wheel     64 Jan 01 18:59 test
```

| 文件类型及权限 | 连接数 | 文件所有者 | 文件所属用户组 | 文件大小 | 文件最后修改时间 | 文件名 |
| -------------- | ------ | ---------- | -------------- | -------- | ---------------- | ------ |
| drwxr-xr-x     | 2      | chenrushan | wheel          | 64       | Jan 01 18:59     | test   |

开头字符代表文件的类型：

- `d` 是目录
- `-` 是文件
- `l` 是软连接文件 (link)

```bash
cd /tmp

touch testln <== 创建一个名为testln的文件

ln -s testln testlnsoft <== 为testln文件创建软链接testlnsoft

ls -l
==> -rw-r--r--  1 chenrushan  wheel      0 Jan 01 19:50 testln
==> lrwxr-xr-x  1 chenrushan  wheel      6 Jan 01 19:51 testlnsoft -> testln
```

`ln -s [源文件或目录] [目标文件或目录]` 创建一个软连接。

## 复制、删除和移动

### cp

```bash
cp [参数] source destination
cp [参数] source1 source2 source3 .... directory

-i  如果目标文件已经存在，在覆盖前会提示是否执行操作
-r  递归复制
-p  连同文件的属性（修改时间，用户ID等）一起复制过去，而非使用默认属性。（适合备份时用）
-u  当 source 比 destination 新时，才更新 destination
```

### rm

`rm` 移除（remove）文件或目录（对比，`rmdir` 是删除空目录）

```bash
rm [参数] 文件或目录

-f  即force，不询问是否删除，文件不存在也不提示警告
-i  会先询问用户是否执行操作
-r  递归删除。危险参数⚠️
```

### mv

`mv` 移动（move）文件、目录，或者重命名

```bash
mv [参数] source target
mv [参数] source1 source2 ... target_directory

-f  即force，即使目标文件已存在，不询问，直接覆盖
-i  如果目标文件已存在，询问是否覆盖
-u  如果目标文件已存在，且 source 较新，才会更新（update）
```

```bash
cd /tmp

touch testmv.txt <== 创建名为testmv.txt的文件

mkdir mvtest

mv testmv.txt mvtest <== 将文件移动到mvtest目录

mv mvtest mvtest2 <== 将刚才的目录名称重命名为mvtest2
```

## 查看文件内容

- `cat`: 由第一行开始显示文件内容
  - `-n` 打印出行号
- `nl`: 显示的时候也显示行号
- `more`: 一页一页的显示文件内容
- `less`: 与more类似
- `head`: 只看头几行
- `tail`: 只看结尾几行

## 创建文件

`touch` 创建文件

```bash
touch test.md <== 创建一个 test.md 文件
```
