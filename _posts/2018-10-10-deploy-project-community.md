---
layout: post
title: "部署项目到服务器 —— 以 Community 为例"
subtitle: "About deployment of Community"
author: "Rushan"
header-img: "img/post-bg-default.jpg"
header-img-credit: "Photo by Andy Holmes on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/LUpDjlJv4_c"
header-mask: 0.4
tags:
  - 部署
  - PM2
---

社区项目：[community](https://github.com/xugy0926/community)

# <span id="catalog">目录

- [前期准备](#prepare)
	- [新增用户](#add-user)
	- [配置SSH 免密登录](#ssh)
	- [更新服务器、安装软件](#server)
- [安装Docker](#docker)
	- [Docker安装MongoDB](#mongodb)
	- [Docker安装Redis](#redis)
	- [Docker安装Nginx](#nginx)
- [宿主机配置node.js、pm2 运行部署环境](#host-setting)
	- [安装node.js](#nodejs)
	- [安装yarn、PM2](#yarn-pm2)
	- [安全设置：安全组](#safety)
	- [测试 Hello World](#test)
- [修改community项目配置文件](#config)
- [pm2自动部署(自动部署卡在Error，目前先手动部署)](#pm2)
	- [修改配置文件](#ecosystem)
	- [拉取源码到服务器](#pull)
	- [部署（暂时手动部署）](#deploy)
	- [如何更新项目](#update)
- [参考资料](#reference)

（看起来有点儿长，💁建议准备好🍉搭配食用）

![server-design](https://ws4.sinaimg.cn/large/006tNbRwgy1fudnv80hczj319i0sw78x.jpg)


# ∮ <span id="prepare">前期准备</span>  [↑](#catalog)

注：建议在本地先把[community](https://github.com/xugy0926/community)跑起来，参考[如何部署community - 李朋](http://xugaoyang.com/post/5ae332a090919f7d042209c5)，然后再考虑部署到服务器，不着急。

关于选服务器，徐帅告诉你：[初学WebApp时，如何选择一台服务器](http://xugaoyang.com/post/59f99291f8ffd52dca8eba09)（Google cloud 可以免费用一年哦~）

服务器操作系统： Ubuntu 16.04 64位

参考：[ Initial Server Setup with Ubuntu 16.04 - DigitalOcean](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)

其他操作系统的也可以在参考资料链接里重选，可能有少部分不同：

![](https://ws1.sinaimg.cn/large/0069RVTdgy1fualz504erj319o0qmn0j.jpg)


## <span id="add-user">新增用户(有sudo权限)</span>  [↑](#catalog)

登录云服务器管理控制台，新服务器记得重置root用户的密码🔑。(为了安全，不要使用简单密码)

💡**Password Tips** ：Mac可以用`pwgen`这个工具生成密码，如果有安装了`brew`，可以用`brew install pwgen`安装。安装之后，在终端执行`pwgen -C 12`就可以生成12位的随机密码，选一个即可。Windows用户可以用生成密码的网站，比如[randomkeygen](https://randomkeygen.com/)。（⚠️**密码要另外记起来**，丢了就找不回来了，可以记在本本📝上，或者用密码工具1Password🔑之类的保存）

登录服务器，用`SSH`，类似`HTTP`，也是一种协议。

在控制台查看自己服务器的 **公网IP**，以root用户登录：

```
// ssh 用户名@你的公网IP
$ ssh root@11.111.111.11
```

输入密码后，登录成功。但是，用root用户来进行开发是很 **危险** 的，一台服务器上如果有跑多个服务，最好每个服务都有各自的用户。所以，用`adduser`新增用户：

(主帅🤴还是不要亲力亲为吧，招人帮自己打理)

```
// 新增用户，用户名自定（root用户状态下，终端符号会变成"#"）
# adduser web
```

设置密码，同样用`pwgen -C 12`就可以生成12位的随机密码，选一个。（密码要另外记起来）

然后提示修改用户信息，可以输入设置，也可以直接按`enter`先用默认信息。

### 让用户有`sudo`权限  [↑](#catalog)

emmm… 现在新增了一个普通用户，由于后续会经常需要用`sudo`命令，普通用户不够权限，所以我们把用户加到`sudo`group里，就有权限啦~ 

root用户登录状态下（或者[其他获得root权限的方式](https://www.digitalocean.com/community/tutorials/how-to-edit-the-sudoers-file-on-ubuntu-and-centos)），执行：

```
# usermod -aG sudo web
```
> 可以用“man魔法”`man usermod`查看manual使用手册(按f、b上下翻页)：
> 
> `usermod` 修改用户账户
> 
> `-a, --append` 仅和`-G`一块使用，将用户添加到附属组群。
> 
> `-G, --groups` 用户所属的群组列表，如果用户当前所在group不在列表中，那用户就会被移出当前group；`-a`可以改变，使用户加入groups，但不被移出当前group。


### （可选）每次执行`sudo`命令都要输入密码，比较麻烦，怎么办？  [↑](#catalog)

参考：

- [How To Modify the Sudoers File - DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-edit-the-sudoers-file-on-ubuntu-and-centos#how-to-modify-the-sudoers-file)
- [让用户在执行sudo的时候不输入密码](http://younglibin.iteye.com/blog/1987403)

我选的方案是：只让某个用户执行`sudo`时不用密码（如果想要其他方案，比如让sudo group里的用户都不用密码，查看参考资料👆）

非root用户登录，用vi编辑器打开`/etc/sudoers`(注意：这个修改只能用vi编辑器)：

```
$ sudo vi /etc/sudoers
```

输入`i`，并`enter`进入编辑模式，

添加`username ALL = NOPASSWD: ALL`这样一行(可以再加入一行注释说明)，其中`username`替换为拥有不用密码权限的用户。

![](https://ws2.sinaimg.cn/large/0069RVTdgy1fubq1akobpj315m0hatak.jpg)

按`esc`回到命令模式，输入`:wq!`以**强制保存**。（注意，`!`感叹号不能省略，因为是只读文件，只能强制保存）。


## <span id="ssh">配置SSH 免密登录</span>  [↑](#catalog)

配置SSH前，要确保server有足够strong💪的密码！

### SSH是谁😳？  [↑](#catalog)
Q: SSH是干啥的？

A: 是一种用于从一台计算机💻安全访问另一台计算机💻的协议。它生成一对秘钥，私钥🔐放本机的`ssh-agent`代理保管，公钥🔐绑定在要访问的远程服务器。当我们想和服务器连接时，秘钥对就会帮我们进行安全验证🔐♥️🔐，我们就可以不用每次登陆都输密码啦😆~ [About SSH - GitHub](https://help.github.com/articles/about-ssh/)

![](https://ws2.sinaimg.cn/large/0069RVTdgy1fu9fzwyjddj31b40o0tit.jpg)
" photo via [How SSH works](https://www.youtube.com/watch?v=zlv9dI-9g1U) "

### SSH怎么用🤔？  [↑](#catalog)

注：有链接的请查看🔗链接的详细步骤，没有链接的会讲解💁。(看英文太辛苦的，可以找Chrome的Google翻译[插件](https://chrome.google.com/webstore/detail/google-translate/aapbdbdomjkkjkaonfhkkikfgjllcleb?hl=zh-CN)帮忙~)

任务：分配2对秘钥，你一把呀~ 我一把~ 🔐🎎🔐 、🔑🎎🔑

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fukgo2ocg4j31540tadld.jpg)

大纲：

- 1.配置“本地私钥🔐” & “服务器公钥🔐”（本地连接服务器的秘钥对A1A2）
	- 1.1 在本地[检查本机是否已有SSH秘钥对](https://help.github.com/articles/checking-for-existing-ssh-keys/#platform-mac)
		- 1.1.1 如果没有，生成新的SSH秘钥对🔐🔐 
	- 1.2 [把私钥添加到本地的ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#adding-your-ssh-key-to-the-ssh-agent)
	- 1.3 把公钥绑定到要访问的远程服务
	- 1.4 测试SSH连接
- 2.配置“服务器私钥🔑” & “GitHub公钥🔑”（服务器连接GitHub的秘钥对B1B2）
	- 2.0 非root用户登录服务器
	- 2.1 在服务器[检查是否有秘钥对](https://help.github.com/articles/checking-for-existing-ssh-keys/#platform-linux)
		- 2.1.1 没有则[生成新秘钥对🔑🔑](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#platform-linux)
	- 2.2 [把私钥添加到服务器的ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#adding-your-ssh-key-to-the-ssh-agent)
	- 2.3 [把公钥添加到GitHub账户](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/#platform-linux)
	- 2.4 [测试SSH连接](https://help.github.com/articles/testing-your-ssh-connection/#platform-linux)
	- 2.5 (补充)git clone项目，然后删掉

---

#### 1.配置“本地私钥🔐” & “服务器公钥🔐”（本地连接服务器的秘钥对A1A2）  [↑](#catalog)

**1.1** 在本地[检查本机是否已有SSH秘钥对](https://help.github.com/articles/checking-for-existing-ssh-keys)

**1.1.1** 如果没有，生成新的SSH秘钥对🔐🔐

用`ssh-keygen`生成秘钥，参数可省略；然后3个`enter`，不给秘钥再加密码。

```
$ ssh-keygen

Generating public/private rsa key pair.
Enter file in which to save the key (/Users/chenrushan/.ssh/id_rsa):[enter]
Enter passphrase (empty for no passphrase):[enter]
Enter same passphrase again:[enter]
```
如果需要更强的key，或者加标签，可以参考[GitHub教程](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)里的：

```
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

- `-t` [-t dsa | ecdsa | ed25519 | rsa] 选择算法，默认就是rsa。关于[算法](https://www.ssh.com/ssh/keygen/) 
- `-b` [-b bits] 选择key size，rsa默认2048位，可以设4096位。 
- `-C` [-C comment] 可以加描述作为标签。github这里写github邮箱是为了标识这是与GitHub连接的秘钥，如果是本机与云服务器的连接秘钥，可自行设定双引号里的标签内容(英文，不建议写中文)。


**1.2** [把私钥添加到本地的ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#adding-your-ssh-key-to-the-ssh-agent)


**1.3** 把公钥绑定到要访问的远程服务

**1.3.1** 🐾复制SSH公钥🔐到剪贴板📋

用`cat ~/.ssh/id_rsa.pub`在终端显示公钥内容，然后手动copy公钥。

或者，在新的本地终端窗口，执行下面的命令进行复制：

```
// Mac用pbcopy，Windows换成clip
$ pbcopy < ~/.ssh/id_rsa.pub
```
（More：参考[xclip、clip xsel和pbcopy的 用法](https://blog.csdn.net/u014166319/article/details/78495244)）


**1.3.2** 把公钥🔐绑定到云服务器实例，登录云平台的⚙控制台：

*如果是阿里云的，参照[导入 SSH 密钥对](https://help.aliyun.com/document_detail/51794.html?spm=a2c4g.11186623.2.4.MwoKE5)，[绑定和解绑 SSH 密钥对](https://help.aliyun.com/document_detail/51796.html?spm=a2c4g.11186623.4.2.T955Bi) 进行导入和绑定。

*如果是腾讯云的，参照[SSH 密钥 - 腾讯云](https://cloud.tencent.com/document/product/213/16691)。

*如果是京东云的，参照[SSH创建和登录](https://www.jdcloud.com/help/detail/346/isCatalog/1) (命令行操作，步骤稍复杂些)。

*其他的，云平台文档搜索"SSH"。


**1.3.3** 非root用户安装SSH公钥

要使用SSH密钥作为非root用户进行身份验证，必须将公钥🔐添加到用户主目录中的特殊文件中。

root用户登录下，用以下命令暂时切换到非root用户：

```
// su - 你的非root名
# su - web
```

(`su`切换成root用户，`su - username`切换到另一用户)

现在就在非root用户的文件夹下了，可以用`pwd`看看自己的位置(😳我是谁，我在哪…)。

新建`.ssh`文件夹，并修改权限：

```
$ mkdir ~/.ssh
$ chmod 700 ~/.ssh
```
（More：关于`chmod`，参考[chmod - wikipedia](https://zh.wikipedia.org/wiki/Chmod)，700代表user拥有rwx(读、写、执行)权限）

用终端文本编辑器在`.ssh`中打开一个名为`authorized_keys`的文件。这里用的编辑器是nano，习惯用vim的童鞋可以按照自己习惯来😉。

```
$ nano ~/.ssh/authorized_keys
```
按照1.3.1的方法复制🐾的公钥内容，`⌘+v`粘贴到这个文件里。

按`ctrl+x`退出，是否保存，按`y`保存修改，最后按`enter`确认文件名。

修改`authorized_keys`权限：

```
$ chmod 600 ~/.ssh/authorized_keys
```
（600表示user拥有rw(读、写)权限）

用`exit`退回root用户。

```
$ exit
```

啦啦啦~ 安装了公钥🔐，可以用SSH密钥以这个非root用户身份登录啦~🍉


**1.3.4** (可选）禁用密码验证（提高安全性，推荐）

> ❗️注意！必须确保【已经为服务器用户安装了公钥🔐（1.3.2、1.3.3）】之后，才能禁用密码验证，不然你就锁定了你的服务器😱

ubuntu.com推荐禁用密码验证：[Disable Password Authentication](https://help.ubuntu.com/community/SSH/OpenSSH/Configuring#Disable_Password_Authentication)

用有sudo权限的非root用户登录：

```
$ ssh web@11.111.111.11
```
用nano(或者vim)打开`/etc/ssh/sshd_config`文件：

```
$ sudo nano /etc/ssh/sshd_config
```

提示输入密码，`enter`，显示文件内容。

搜索（用`ctrl+w`），或者翻页（用`ctrl+y`到上一页，`ctrl+v`到下一页），找到`PasswordAuthentication`所在的行，改成这样（注意，前面没有#）：

```
PasswordAuthentication no
```
![](https://ws3.sinaimg.cn/large/0069RVTdgy1fuahjy8dw4j315w0higpz.jpg)

![](https://ws2.sinaimg.cn/large/0069RVTdgy1fuahk083ozj315k0fiq5f.jpg)

跟前面一样，按`ctrl+x`退出，是否保存，按`y`保存修改，`enter`。

（可以用`cat /etc/ssh/sshd_config`再看看内容，检查下有没改对。）

既然改了sshd的配置，那就要重新加载下嘛~ 重新加载SSH daemon：

```
$ sudo systemctl reload sshd
```


**1.4** 测测SSH连接

**1.4.1** root用户登录看看

```
$ ssh root@11.111.111.11
```

第一次登录时，本地计算机还不认识服务器，所以系统会询问是否确定要继续连接：

```
The authenticity of host '203.0.113.0 (203.0.113.0)' can't be established.
ECDSA key fingerprint is SHA256:IcLk6dLi+0yTOB6d7x1GMgExamplewZ2BuMn5/I5Jvo.
Are you sure you want to continue connecting (yes/no)? 
```
大胆输入`yes`，然后`enter`。秘钥指纹存好了，收到已经把服务器加到已知主机列表的确认信息🎉（第一次见面后，把服务器加到通讯录了，以后就是好朋友了👫）：

```
Warning: Permanently added '203.0.113.0' (ECDSA) to the list of known hosts.
```


**1.4.2** 非root用户登录呢

```
$ ssh web@11.111.111.11
```
原本没有1.3.3，1.3.4，提示请输入密码。emmm… 好的，我已经补充了步骤👆

做完了再来试，不用密码成功登录~ 🎉

---

（有没有晕😳，到这里，完成了【1. 配置“本地私钥🔐” & “服务器公钥🔐”】。接下来是【2. 配置“服务器私钥🔐” & “GitHub公钥🔐”】，做过了1，那2就简单得不要不要的，开不开心，激不激动?! …… ）

（不知道自己在哪😶？吃🍉歇一歇。可以翻到前面看看大纲👆）

---

#### 2.配置“服务器私钥🔑” & “GitHub公钥🔑”（服务器连接GitHub的秘钥对B1B2）  [↑](#catalog)

为了让`pm2`能顺利拉取到GitHub（或码云）上的源码，需要配置SSH，让服务器到GitHub（或码云）可以用ssh秘钥对验证，免密登录。

**2.0** 非root用户登录服务器

登录服务器，切换目录：

```
$ ssh web@11.111.111.11

$ cd ~
```
⚠️注：接下去步骤的终端都是**服务器终端**，即要先登录服务器，而**不是本地终端**。（明明是server和GitHub的牵手，不要让local machine来插手🙈）

**2.1** 在服务器[检查是否有秘钥对](https://help.github.com/articles/checking-for-existing-ssh-keys/#platform-linux)

**2.1.1** 没有则[生成新秘钥对](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#platform-linux)

**2.2** [把私钥添加到服务器的ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#adding-your-ssh-key-to-the-ssh-agent)

**2.3** [把公钥添加到GitHub账户](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

注：在服务器终端复制公钥，建议使用最通用的方法，`cat ~/.ssh/id_rsa.pub`显示公钥，然后手动复制。而不是用`xclip`，会遇到坑[`Error: Can't open display: (null)`](https://askubuntu.com/questions/305654/xclip-on-headless-server)。

**2.4** [测试SSH连接](https://help.github.com/articles/testing-your-ssh-connection/#platform-linux)

**2.5** (补充)git clone项目，然后删掉

测试完之后，还有一件事。记不记得前面1.4.1，本地主机第一次用SSH秘钥连接服务器，把服务器加到了(通讯录)`known hosts`？现在服务器应该还没把GitHub加到“通讯录”呢，所以需要“一次正式的见面”。我怎么知道？测试的时候没有加么？emmm… 因为没收到`Permanently added … to the list of known hosts`的提示。

上面一段是我对马成教程里说要`git clone`项目，然后又删掉，这一步的作用的猜测。

`git clone`任意仓库（用ssh，而不是HTTPS的URL）(可以clone一个比较小的项目，比较快)：

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fudzwzhlsmj31j00ey78z.jpg)

```
// 记得替换ssh链接
$ git clone git@github.com:username/repository.git
```
clone过程会看到：

```
Warning: Permanently added the RSA host key for IP address '13.229.188.59' to the list of known hosts.
```

对！它说服务器认识把你的GitHub了，并加到了(通讯录)`known hosts`，以后就是好朋友了👫！

然后用`rm -r`把项目删掉：

```
// 记得替换仓库名称
$ rm -r repository
```


## <span id="server">更新服务器、安装软件</span>  [↑](#catalog)

关于各系统下的包管理工具：

![](https://ws3.sinaimg.cn/large/0069RVTdgy1fubl8lox72j315k0d4dhe.jpg)
" photo via [Package Management Basics: apt, yum, dnf, pkg - DigitalOcean](https://www.digitalocean.com/community/tutorials/package-management-basics-apt-yum-dnf-pkg) "

`apt-get`如何使用：[How To Manage Packages In Ubuntu - DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-manage-packages-in-ubuntu-and-debian-with-apt-get-apt-cache)

### 更新服务器  [↑](#catalog)

服务器用户登录，重新同步包索引文件，获取更新信息（在正式安装更新之前都要执行这一步）:

```
$ sudo apt-get update
```
安装更新：

```
$ sudo apt-get upgrade
```

### 安装git  [↑](#catalog)

安装常用软件，比如git：

```
$ sudo apt-get install git
```

注：服务器和本地都需要安装git最新版，否则后面用pm2自动部署时，可能有代码无法及时同步的问题，服务器始终无法 pull 到最新版本的代码。所以顺便检查下本地的git，有新版本记得更新。

---
##### --------- 我是🙈休息一下🐒的分割线 --------- #####
---

# ∮ <span id="docker">安装Docker</span>  [↑](#catalog)

在服务器(Ubuntu 16.04)安装Docker，参考：

- [在阿里云ESC上快速搭建mongodb/redis - 徐高阳](http://xugaoyang.com/post/5a42514147125b226734c074)
- [Get Docker CE for Ubuntu - Docker官网](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
- [How To Install and Use Docker on Ubuntu 16.04 - DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04)

如果你有一台阿里云ECS服务器，那就按第一个链接，高阳老师的教程，方便高效。(安装得太快就像龙卷风🌪 简直棒呆了😆♥️♥️)

如果不是阿里云ECS：参考Docker官网和DigitalOcean的教程安装Docker CE。

安装完成后，可以用`sudo docker run hello-world`测试一下是否安装正确。有看到`Hello from Docker!`就说明安装成功。

### 配置Docker加速器  [↑](#catalog)

国内网络原因，后续拉取Docker镜像会十分缓慢，所以推荐配置加速器。

- 登录道云，[DaoCloud](https://www.daocloud.io/mirror#accelerator-doc)，拿到命令；
- 在服务器终端执行命令，自动配置Docker加速器；
- 在配置完成后，根据终端中的提示重启docker，以使配置生效。

![](https://ws1.sinaimg.cn/large/0069RVTdgy1fubt936rrlj31kw0h7jxd.jpg)

## <span id="mongodb">Docker安装MongoDB</span>  [↑](#catalog)

### Docker的数据管理  [↑](#catalog)

![](https://ws2.sinaimg.cn/large/0069RVTdgy1fubuqve9nbj30dy073weu.jpg)

" photo via [Docker](https://docs.docker.com/storage/volumes/) "


- 数据卷(volume) (持久化数据，数据备份、还原、迁移☑️)
- 挂载宿主机目录(bind mount)
- tmpfs挂载(tmpfs mount)
- 容器可写层里的数据(container’s writable layer)

volume的内容在容器(container)的生命周期之外，也就是说，即使删掉容器，volume依旧在。

👨‍💻‍马成教程里选volume的理由是：

“将容器中的数据库文件映射到宿主机中,这样做的好处就是，如果数据库报错或者一些配置错误,就可以直接`docker rm mongo`重新一个容器,而数据库文件是保存在宿主机中并不会丢失,套用一句术语就是让宿主机与docker容器解耦。”👏

### 创建数据卷(volume)  [↑](#catalog)

创建2个数据卷，名字叫`v-mongo-db`和`v-mongo-log`:

(运行docker的命令都需要`sudo`)

```
$ sudo docker volume create v-mongo-db

$ sudo docker volume create v-mongo-log
```

可以用`sudo docker volume inspect`看看数据卷的信息：

```
$ sudo docker volume inspect v-mongo-db

// 
[
    {
        "CreatedAt": "2018-08-16T21:38:15+08:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/v-mongo-db/_data",
        "Name": "v-mongo-db",
        "Options": {},
        "Scope": "local"
    }
]
```

### 启动mongo容器  [↑](#catalog)

从`mongo`镜像启动一个容器，并指定前面2个数据卷映射到容器内的特定位置：

```
$ sudo docker run -d -p 27017:27017 -v v-mongo-db:/data -v v-mongo-log:/var/log/mongodb --privileged --name mongodb mongo
```

- `-d` --detach 使容器在后台运行，打印新容器ID
- `-p 27017:27017` --publish hostPort:containerPort 容器的端口27017映射到，宿主机的端口27017。这样外部就可以访问容器内的网络应用了。
- `-v v-mongo-db:/data` -v volumeName:/CONTAINER-DIR 指定宿主机的数据卷`v-mongo-db`映射到，容器的mongodb默认数据库文件的存放地址`/data`(绝对路径)
- `-v v-mongo-log:/var/log/mongodb` 指定宿主机的数据卷`v-mongo-log`映射到，容器的mongodb日志的存放地址`/var/log/mongodb`(绝对路径)
- `--privileged` 赋予容器操作权限
- `--name mongodb` 为容器起个名字
- `mongo` 指定创建容器的镜像

启动容器后可以用`sudo docker ps`查看运行中的容器列表。

### 如何操作docker里的MongoDB  [↑](#catalog)

安装 mongodb-clients

有了mongodb服务器，我们还可以安装一个mongodb的客户端

```
$ apt install mongodb-clients
```

安装成功后，通过客户端指令，连接到mongodb服务:

```
$ mongo 
```

此时你就能看到连接的信息。终端光标变为`>`，即可输入mongodb的命令行进行操作。

更多操作参考：

- [MongoDB - Basic Commands 【译稿】 - 大师姐](http://xugaoyang.com/post/5a1bf5be87749a7d36d660b3)
- [mongo基础语法。增、删、改、查 - 谢玉辉](http://xugaoyang.com/post/5a26b66335793a02204c3ae2)
- [服务器 mongodb 设置权限 - 谢玉辉](http://xugaoyang.com/post/5a26691935793a02204c3ade)


### 开放服务器的27017端口  [↑](#catalog)

为了让其他电脑快速的连接数据库，这时，就需要开放服务器27017端口。参考：[添加安全组规则 - Aliyun](https://help.aliyun.com/document_detail/25471.html?spm=a2c4g.11186623.4.5.CqDYiz)

1. 给服务器添加一个安全组。
2. 在服务器安全组的【入方向】增加访问27017的安全组规则。

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fucut2ev47j31kw0sagxb.jpg)

## <span id="redis">Docker安装Redis</span>  [↑](#catalog)

为什么数据库还有`Redis`？它是把常用的数据库放在内存缓存里，这样会更快地响应`I/O`请求。

在Docker容器市场，选择[bitnami/redis](https://hub.docker.com/r/bitnami/redis/)，有介绍如何安装**无密码**的Redis缓存容器：

```
$ sudo docker run -d -p 6379:6379 --name redis -e ALLOW_EMPTY_PASSWORD=yes --privileged bitnami/redis:latest
```

- `-e ALLOW_EMPTY_PASSWORD=yes` --env 设置环境变量，允许空密码（注：此env变量仅建议用于**测试或开发**目的。建议为任何其他方案指定密码REDIS_PASSWORD。）

### 开放服务器的6379端口  [↑](#catalog)

redis的安全组规则也可以直接在协议类型选：

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fucv03k2xzj31kw0rpdst.jpg)

## <span id="nginx">Docker安装Nginx</span>  [↑](#catalog)

使用IP地址和端口组合进行调试，没问题，需要注意的是，root用户才有权限取得1024端口以下的端口，而其他用户是没有这个权限的。
一般我们写程序也不指定80端口，而是使用反向代理，使用nginx将监听的80端口，映射到程序指定的端口处理。

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fujtu1wzjfj30sg0gk40y.jpg)
" photo via [An Introduction to NGINX - Medium](https://medium.freecodecamp.org/an-introduction-to-nginx-for-developers-62179b6a458f) "

因为nginx需要改动配置文件,每次都去容器中更改比较麻烦；还有依据让容器与宿主机解耦的原则，需要在宿主机上创建nginx的配置文件。所以，选择用挂载宿主机目录(bind mount)的方式管理nginx的文件。

### 宿主机创建目录及配置文件  [↑](#catalog)

-  在宿主机创建目录

```
$ sudo mkdir -p /home/nginx1/
$ sudo mkdir -p /home/nginx1/conf.d
```
**注：conf.d是一个文件夹**

-  在宿主机中创建配置  

（同样，编辑器用nano还是vim看个人习惯。这里是用nano）

```
$ sudo nano /home/nginx1/nginx.conf
```

-  下面的配置不用改动,直接粘贴上去即可(粘贴的时候注意不要漏掉字符)

```
user  nginx;
worker_processes  1;
error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;
events {
    worker_connections  1024;
}
http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile        on;
    #tcp_nopush     on;
    keepalive_timeout  65;
    #gzip  on;
        proxy_redirect          off;
        proxy_set_header        Host $host;
        proxy_set_header        X-Real-IP $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        client_max_body_size    10m;
        client_body_buffer_size   128k;
        proxy_connect_timeout   5s;
        proxy_send_timeout      5s;
        proxy_read_timeout      5s;
        proxy_buffer_size        4k;
        proxy_buffers           4 32k;
        proxy_busy_buffers_size  64k;
        proxy_temp_file_write_size 64k;
        upstream fn {
                server 0.0.0.0:3000;
        }
        server {
        listen       80;
        server_name  127.0.0.1;
        location / {
            proxy_pass   http://fn;
            index  index.html index.htm;
        }
    }
}
```

同样，`ctrl+x`退出编辑，`y`选择保存，`enter`确认文件名。

### 启动nginx容器  [↑](#catalog)

从nginx镜像启动一个nginx容器，且用`bind mount`挂载宿主机的2个目录到容器：

```
$ sudo docker run -it -d --name nginx -v /home/nginx1/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx1/conf.d:/etc/nginx/conf.d --net=host  --privileged nginx

```

- `-v /home/nginx1/nginx.conf:/etc/nginx/nginx.conf` -v /HOST-DIR:/CONTAINER-DIR 挂载宿主机的`/home/nginx1/nginx.conf`目录，到容器的`/etc/nginx/nginx.conf`目录(绝对路径)。
- `-v /home/nginx1/conf.d:/etc/nginx/conf.d` 同上。
- `--net=host` 让容器使用宿主机的网络配置。搭配`--privileged`一起用，容器会被允许直接配置主机的网络堆栈(network stack)。[network](https://yeasy.gitbooks.io/docker_practice/underly/network.html)

用`sudo docker ps`看看正在运行的容器列表。

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fuct4soy31j31ju04sta4.jpg)

---
##### --------- 我是🙈休息一下🐒的分割线 --------- #####
---

# ∮ <span id="host-setting">宿主机配置node.js、pm2 运行部署环境</span>  [↑](#catalog)

- 动态型编程语言：需要在服务器上安装运行环境，比如node.js、python、ruby。
- 静态型编程语言：需要在开发环境安装编译环境，不需要在服务器上安装，只把编译后的可执行文件放到服务器上执行，比如C、C++、Golang。

开发环境用`nvm`安装node，但在正式的生产环境很少用。因为`nvm`有些弊端，比如将nvm安装在用户目录，其他用户就用不了，给后期的维护造成麻烦。因此，生产环境就参考node官网 [Installing Node.js via package manager](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions) 的方法。

## <span id="nodejs">安装node.js<span>  [↑](#catalog)

这里用官网方法安装node，在Ubuntu下安装8.x版本的node.js(community要求node版本>=8.9.1)：

```
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ sudo apt-get install -y nodejs
```


## <span id="yarn-pm2">安装yarn、pm2</span>  [↑](#catalog)

[PM2](https://pm2.io/doc/zh/runtime/quick-start/)，进程管理程序(process manager)，它可以在程序意外崩溃的时候自动重启，可以查日志找原因，而且它还可以管理多个项目，很方便。

[yarn](https://yarnpkg.com/zh-Hans/docs/usage)，和npm一样是包管理器，速度会更快些。

安装`yarn`、`pm2`，在这个项目里，个人建议只用`yarn`，不用`npm`。同一个项目里混用包管理工具，容易出现问题。

按照[yarn官网的方法](https://yarnpkg.com/lang/en/docs/install/#debian-stable)，选Ubuntu系统，在服务器安装`yarn`：

```
$ curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
$ echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

$ sudo apt-get update && sudo apt-get install yarn
```

为了安装速度更快，设置镜像源为淘宝镜像：

```
// 查看当前镜像
$ yarn config get registry
// 是yarn的原镜像，安装慢
-> https://registry.yarnpkg.com

// 换成淘宝镜像
$ yarn config set registry https://registry.npm.taobao.org --global
```

用`yarn global`全局安装pm2:

```
$ yarn global add pm2
```

### 看看pm2能不能用  [↑](#catalog)

执行`pm2 -v`查看pm2版本。如果正常返回版本，那恭喜你~ 

如果出现异常：

```
No command 'pm2' found, did you mean:
 Command 'wm2' from package 'wm2' (universe)
 Command 'pms' from package 'pms' (universe)
 Command 'pmk' from package 'pmk' (universe)
 Command 'pmw' from package 'pmw' (universe)
 Command 'fpm2' from package 'fpm2' (universe)
 Command 'pom2' from package 'libpod-pom-perl' (universe)
 Command 'pmi' from package 'powermanagement-interface' (universe)
 Command 'pm' from package 'powerman' (universe)
pm2: command not found
```

1.先试试修改`~/.bashrc`

```
$ nano ~/.bashrc
```

注释掉下面几行，pm2在服务器上使用的是非交互的连接方式，注释掉避免提前返回：

```
# If not running interactively, don't do anything
#case $- in
#   *i*) ;;
#      *) return;;
#esac
```

然后`source ~/.bashrc`重新加载。再试试`pm2 -v`。

2.如果还不能解决，那就把`yarn global bin`的path加到环境变量`PATH`：

A. 如果你只希望 **当前用户** 可以访问`yarn global`安装的包，那就编辑当前用户的`~/.bashrc`:

```
// 编辑~/.bashrc，作用于当前用户
$ nano ~/.bashrc
```

B. 如果你希望 **所有用户** 都可以访问`yarn global`安装的包，那就编辑`/etc/bash.bashrc`（需要`sudo`）:

```
// 编辑/etc/bash.bashrc，作用于all users
$ sudo nano /etc/bash.bashrc
```

A / B 选一个(个人选的是A)，光标向下移动到内容结尾，加入下面这一行，表示在`$PATH`(当前的PATH值)后加你的path：

```
export PATH="$PATH:$(yarn global bin)" 
```

- `:`是路径之间的分隔符。
- `$()`是命令替换([Command Substitution](https://www.gnu.org/software/bash/manual/html_node/Command-Substitution.html))，括号内命令的输出值 会替换 命令本身。
- `yarn global bin`这个命令是输出路径。

退出保存后，用`source`重新加载；输出当前`PATH`变量的值，看看结尾有没有`/home/username/.yarn/bin`：

```
// 如果选的是A
$ source ~/.bashrc

// 如果选的是B
$ source /etc/bash.bashrc

// 输出$PATH
$ echo $PATH

// output
/home/web/bin:/home/web/.local/bin:/home/web/bin:/home/web/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/home/web/.yarn/bin
```

参考：[PATH - stackoverflow](https://stackoverflow.com/questions/11650840/linux-remove-redundant-paths-from-path-variable)、[Session-wide environment variables - Ubuntu](https://help.ubuntu.com/community/EnvironmentVariables#Persistent_environment_variables)

注：本地也要记得安装node、yarn、pm2 ~


## <span id="safety">安全设置：安全组</span>  [↑](#catalog)

前面安装MongoDB和Redis时已经加了2条规则，按照前面的方法，看看还有哪些端口还没开放，把还没有的安全组规则加上吧~

![安全组](https://ws4.sinaimg.cn/large/006tNbRwgy1fucv3c0r7oj315v0e3t93.jpg)

## <span id="test">测试 Hello World</span>  [↑](#catalog)

新建并打开一个app.js文件：

```
$ nano ~/app.js
```
写入node.js官网的[测试代码](https://nodejs.org/en/docs/guides/getting-started-guide/)：

```
const http = require('http');

const hostname = '127.0.0.1';const port = 3000;

const server = http.createServer((req, res) => {
res.statusCode = 200;
res.setHeader('Content-Type', 'text/plain');
res.end('Hello World\n');});

server.listen(port, hostname, () => {
console.log(`Server running at http://${hostname}:${port}/`);});
```
退出编辑并保存后，尝试用pm2启动：

```
$ pm2 start app.js
```
打开浏览器，访问服务器(公网)ip，或者域名(域名需要事先正确解析)(阿里云的，如果域名访问有问题，可以用[阿里云检测](https://zijian.aliyun.com/?spm=5176.200146.1001.3.iOx0X8))，能看到:

> Hello World

就表示nginx配置，pm2，node.js运行环境配置成功了！🎉🎉🎉🍉

测试完，用`pm2 kill`关闭守护线程(Daemon)。

---
##### --------- 我是🙈休息一下🐒的分割线 --------- #####
---

# ∮ <span id="config">修改community项目配置文件</span>  [↑](#catalog)

参考：

- [修改community配置文件 - 李鹏](http://xugaoyang.com/post/5ae332a090919f7d042209c5)
- [部署社区项目笔记 - 何伟](http://xugaoyang.com/post/5aad1eb5b1745b11c007ffff)

把你fork的community项目`git clone`到本地，编辑器打开，准备改文件。

![clone](https://ws2.sinaimg.cn/large/006tNbRwgy1fufy5g5gnnj31kw0zxql9.jpg)

mongo的url、redis的host：可以先不用`127.0.0.1`这样的本机ip，而用服务器(公网)ip地址（好处是，便于调试，在本地运行项目可以访问到服务器数据库）：

```
  mongodb: {
    url: 'mongodb://47.xxx.xxx.xx:27017/community'
  },

  redis: {
    host: '47.xxx.xxx.xx',
    port: 6379, //6379,
    db: 0,
    password: ''
  },
```

[七牛云](https://www.qiniu.com/)，(用来存储上传的图片)登录，管理控制台，对象存储，新建你的存储空间。

qn的`bucket`就是空间的名字；`origin`就是测试域名；`uploadURL`如下:

![qn](https://ws1.sinaimg.cn/large/006tNbRwgy1fufyhdmb6cj31kw0j5ahg.jpg)

```
  qn: {
    accessKey: 'axxxxxxxxxxxxxxxxxxxxxxxxxxxxxx', 
    secretKey: 'sxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx',
    bucket: ' js-playground',
    origin: 'p657845d8.bkt.clouddn.com', // 用你的测试域名，每个域名有日流量限制
    uploadURL: 'http://upload.qiniu.com/' // 就填这个
  } 
```

GitHub第三方登录，先[在github注册一个Oauth application](http://xugaoyang.com/post/5ae3258a90919f7d042209c2)，`clientId` `clientSecret`就在`Settings/Developer settings/OAuth Apps`里，callback URL的写法是：`http://你的域名/auth/github/callback`路径是根据`pageRouter.js`的：

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fufyv9n45rj31go09i774.jpg)

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fufz0m99ngj31kw094dkd.jpg)

---
##### --------- 我是🙈休息一下🐒的分割线 --------- #####
---

# ∮ <span id="pm2">pm2自动部署</span>  [↑](#catalog)

## <span id="ecosystem">修改配置文件`ecosystem.json`</span>  [↑](#catalog)

修改配置文件`ecosystem.json`文件：

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fulpak1wq4j31kw0zxk8s.jpg)

### 新建community项目文件夹  [↑](#catalog)

非root用户登录服务器，递归新建文件夹`/var/www/production/community`：

```
$ sudo mkdir -p /var/www/production/community
```

修改权限，让pm2可以操作文件：

```
$ sudo chmod 777 -R /var/www/production
```
- `777` 表示User、Group和Other用户都拥有rwx（读写执行）权限。（七窍全开…👻）
- `-R` 递归修改

## <span id="pull">拉取源码到服务器</span>  [↑](#catalog)

刚才修改了配置文件，在vscode提交commit（但是注意👇），然后`git push`。

> `index.dev.js`配置文件里有很多key，如果直接提交commit到GitHub，相当于公开。不可以。
> 
> 方案1：GitHub的私密仓库(付费)，或者码云(免费)
> 
> 方案2：不上传到GitHub，之后登录服务器加配置文件。我是这样做的：
> 
> 在`.gitignore`文件结尾加一行`index.dev.js`；把修改好的`index.dev.js`备份到其他地方，然后删除community里的`index.dev.js`；再提交commit和`git push`。这样就取消track配置文件。再把备份的`index.dev.js`拷回community，以后git就不再跟踪它的变化。

(在本地vscode的终端，community项目下)用pm2初始化服务器里的远程文件夹，执行命令会自动从GitHub拉取源码到服务器：

```
$ pm2 deploy ecosystem.json production setup
```

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fug0ebcy0tj31kw0fq7dg.jpg)

(如果前面选的是方案1，则不用补文件)补上配置文件：非root用户登录服务器，进入community源码文件夹，新建`index.dev.js`：

```
$ cd /var/www/production/community/source/src/config

$ nano index.dev.js
```

把前面改好的`index.dev.js`文件里的内容复制粘贴过去，然后`ctrl+x`退出编辑，`y`保存，`enter`确认文件名。

## <span id="deploy">部署（暂时手动部署）</span>  [↑](#catalog)

在本地终端，community项目下，执行：

```
$ pm2 deploy ecosystem.json production
```

如果你也卡在这个Error…

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fuiou9e08fj31kw0sxqci.jpg)

暂时搁置自动部署，先手动部署：

在服务器终端：

```
// 切换到源码所在文件夹
$ cd /var/www/production/community/source

// 安装依赖
$ yarn

// 创建生产环境的配置文件src/config/index.pro.js，config是package.json里定义的脚本
$ yarn run config
```
修改配置文件`index.pro.js`，内容跟`index.dev.js`一样。

在生产环境运行项目

```
// build是package.json里定义的脚本
$ yarn run build

// 环境变量选择生产环境，80端口
$ NODE_ENV=production PORT=80

// pm2启动项目(将app注册到进程列表，并后台启动)
$ pm2 start dist/app.js
```

查看进程列表、项目运行日志([日志管理 - pm2](https://pm2.io/doc/zh/runtime/guide/log-management/))：

```
$ pm2 ls
$ pm2 logs
```

在浏览器用访问IP或者域名地址，看看项目有没跑🏃起来。(阿里云的，如果域名访问有问题，可以用[阿里云检测](https://zijian.aliyun.com/?spm=5176.200146.1001.3.iOx0X8))

停止项目运行、再启动项目([进程管理 - pm2](https://pm2.io/doc/zh/runtime/guide/process-management/))：

```
// stop命令停止了进程，但保留在进程列表，只是status变成stopped
$ pm2 stop app

// 再启动进程，就不用再pm2 start dist/app.js了，因为还在列表里，pm2 ls可以看进程列表
$ pm2 start app
```


### 【TroubleShooting】  [↑](#catalog)

如果中间`pm2`启动报错:

```
/var/www/production/community/source/dist$ pm2 start app.js
path.js:1167
          cwd = process.cwd();
                        ^

Error: ENOENT: no such file or directory, uv_cwd
    at Object.resolve (path.js:1167:25)
    at Function.Module._resolveLookupPaths (module.js:424:17)
    at Function.Module._resolveFilename (module.js:541:20)
    at Function.Module._load (module.js:474:25)
    at Module.require (module.js:596:17)
    at require (internal/module.js:11:18)
    at Object.<anonymous> (/home/web/.config/yarn/global/node_modules/pm2/bin/pm2:11:17)
    at Module._compile (module.js:652:30)
    at Object.Module._extensions..js (module.js:663:10)
    at Module.load (module.js:565:32)
```

参考：[pm2 - github](https://github.com/Unitech/pm2/issues/2057)

> PM2 daemon's working directory should not be in removable directory.

注意执行`pm2`的目录。

```
$ cd /var/www/production/community/source
$ pm2 start dist/app.js
```

## <span id="update">如何更新项目</span>  [↑](#catalog)

配置文件(`index.dev.js` `index.pro.js`)以外的修改，在本地community进行，提交commit之后，push到GitHub；在服务器终端拉取更新，重启服务：

```
$ cd /var/www/production/community/source
$ git pull
$ yarn
$ yarn run build
$ pm2 stop app
$ pm2 start app
```

---
##### --------- 我是🙈终于到了结尾🐒的分割线 --------- #####
---

如果你也跑起来了，那真是欢喜🍉🍉🍉！！这是一个起点，接下来可以研究研究项目，怎么改成自己想要的网站的，比如博客。

---

# ∮ <span id="reference">参考资料</span>   [↑](#catalog)

♥️ 谢谢各位棒棒的教程：

- [使用docker容器部署community项目 - 马成](http://xugaoyang.com/post/5b66bc5656d79a569d97ca4f#)
- [Initial Server Setup with Ubuntu 16.04 - DigitalOcean](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)
- [如何部署服务器 - 黄家树](http://xugaoyang.com/post/5a09c178ae929550231e42bd)
- [在阿里云ESC上快速搭建mongodb/redis - 徐高阳](http://xugaoyang.com/post/5a42514147125b226734c074)
- [配置 PM2 实现代码自动发布 - 何伟](http://xugaoyang.com/post/5ab654db4787ca08b2595ccb)
- [Docker 基础命令 - Docker](https://docs.docker.com/engine/reference/commandline/docker/)
- [Deployment - PM2](http://pm2.keymetrics.io/docs/usage/deployment/)

发布于 [JavaScript社区](https://xugaoyang.com/post/maR07Ndsf5)。