---
layout: post
title: "用Docker跑node.js代码"
subtitle: "About "
author: "Rushan"
header-img: "img/post-bg-docker.png"
header-img-credit: "Buddy"
header-img-credit-href: "https://buddy.works/guides/docker"
header-mask: 0.4
tags:
  - JavaScript
  - Docker
---

[用docker跑nodejs代码 — JavaScript社区](https://xugaoyang.com/post/e6D10RTr0v)

参考资料：

- 🌰 来源：[Dockerizing a Node.js web app — Node.js](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)
- Docker官方文档：[Docs — Docker](https://docs.docker.com/get-started/)
- Docker命令行(CLIs)：[Docker CLI — Docker](https://docs.docker.com/engine/reference/commandline/build/)
- [Docker - 从入门到实践 — Gitbook](https://yeasy.gitbooks.io/docker_practice/)

## Docker基础概念

参考：[Docker concepts](https://docs.docker.com/get-started/#docker-concepts)

### 镜像(image)和容器(container)

容器通过运行镜像来启动。

**镜像** 是一个可执行包(executable package)，包含运行一个app所需的所有东西 —— 代码，运行时，库，环境变量和配置文件。（静态）

**容器** 是镜像的运行时实例(runtime instance) —— 镜像执行时在内存中的实体（即，有状态的镜像，或者用户进程 ）。（动态，可以被创建、启动、停止、移动、删除等）

---

🌰：在Docker中运行一个简单的Node.js webapp。来源：[Dockerizing a Node.js web app — Node.js](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)

下面在原文例子上，会有一些补充说明，说明部分会以引用的形式出现，结尾标注‘@’。

## 创建Node.js app

确认安装好Docker后。

新建一个文件夹，来放这个项目的文件，在文件夹里新建`package.json`文件，用来描述这个app以及它的依赖：

```js
{
  "name": "docker_web_app",
  "version": "1.0.0",
  "description": "Node.js on Docker",
  "author": "First Last <first.last@example.com>",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.16.1"
  }
}
```

有了`package.json`，在终端执行`npm install`，如果你的`npm`版本是5或者更新的，就会生成一个`package-lock.json`文件，它将会被复制到你的Docker image（镜像）。

然后，新建`server.js`文件，定义一个使用[Express.js](https://expressjs.com/)框架的web app：

```js
'use strict';

const express = require('express');

// Constants
const PORT = 8080;
const HOST = '0.0.0.0';

// App
const app = express();
app.get('/', (req, res) => {
  res.send('Hello world\n');
});

app.listen(PORT, HOST);
console.log(`Running on http://${HOST}:${PORT}`);
```

接下来，看看如何用官方Docker镜像在Docker容器中运行这个app。首先，要为这个app构建一个Docker镜像。

## 创建Dockerfile

用`Dockerfile`定制镜像。

参考：
- 使用 Dockerfile 定制镜像：[Dockerizing a Node.js web app — Node.js](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)
- [Dockerfile 指令详解](https://yeasy.gitbooks.io/docker_practice/image/dockerfile/)

> Docker是分层存储的架构。镜像构建时，会一层层构建，前一层是后一层的基础。@

> `Dockerfile` 是一个文本文件，其内包含了一条条的**指令**(Instruction)，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。@

---

在终端新建一个空文件`Dockerfile`：

> touch Dockerfile

在编辑器打开`Dockerfile`，写入指令。

第一步，我们需要定义，要以哪个镜像为基础，来构建我们的镜像。

这里我们用的是最新的的LTS version 8 的node，镜像存储在[Docker Hub](https://hub.docker.com/)。

```docker
FROM node:8
```

> FROM 指定基础镜像

第二步，创建一个目录来保存镜像中的app代码，这将是app的工作目录：

```docker
# Create app directory
WORKDIR /usr/src/app
```

> `WORKDIR` 制定工作目录（或者称为当前目录）
> 
> 以后各层的当前目录就被改为指定的目录，如该目录不存在，WORKDIR 会帮你建立目录。@

这个镜像已经安装了Node.js和NPM，下一步，就是用`npm`安装app的依赖。请注意，如果你的node版本是4或更早的，那么`package-lock.json`文件将不会被生成。

```docker
# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN npm install
# If you are building your code for production
# RUN npm install --only=production
```

请注意，我们只复制`package.json`文件，而不是复制整个工作目录。这样我们可以利用Docker的缓存层。[bitJudo](http://bitjudo.com/blog/2014/03/13/building-efficient-dockerfiles-node-dot-js/)很好地解释了这一点。

> `COPY <源路径>… <目标路径>`
> 
> `COPY` 指令将从构建上下文(context)目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置。
> 
> 什么是[镜像构建上下文（Context）](https://yeasy.gitbooks.io/docker_practice/image/build.html#%E9%95%9C%E5%83%8F%E6%9E%84%E5%BB%BA%E4%B8%8A%E4%B8%8B%E6%96%87%EF%BC%88context%EF%BC%89) @

> `RUN` 执行命令 @

将app的源代码捆绑到Docker镜像里，用`COPY`指令：

```docker
# Bundle app source
COPY . .
```

app要绑定到8080端口，要使用`EXPOSE`指令，让docker daemon映射它：

```docker
EXPOSE 8080
```

> `EXPOSE` 声明端口 @

最后，用`CMD`定义运行app时使用的命令。这里我们会用最基本的`npm start`来运行`node server.js`，启动服务器：

```docker
CMD ["npm", "start"]
```

> exec格式：`CMD ["可执行文件", "参数1", "参数2"...]`
> 
> `CMD` 容器启动命令 @

写完指令，`Dockerfile`应该是这样：

```docker
FROM node:8

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN npm install
# If you are building your code for production
# RUN npm install --only=production

# Bundle app source
COPY . .

EXPOSE 8080
CMD [ "npm", "start" ]
```

## 创建.dockerignore 文件

在同个文件夹里新建一个`.dockerignore`文件，其中包含以下内容：

```
node_modules
npm-debug.log

.DS_Store
```

这样可以防止将本地moudules和debug logs复制到你的Docker镜像，且可能覆盖镜像中已安装的modules。

> 这个不就像git的`.gitignore`么~ 这里我加了个`.DS_Store`，Mac自动生成的隐藏文件。
> 文档：[.dockerignore file](https://docs.docker.com/engine/reference/builder/#dockerignore-file) @

## 构建你的镜像

转到有`Dockerfile`的目录下，终端运行以下命令来构建Docker镜像。使用`-t`(tag)可以标记镜像(可自行修改`<yourusername>`)，以后用`docker images`命令时更容易找到：

```bash
$ docker build -t <your username>/node-web-app .
```

构建的过程会像这样（分层构建）：

```bash
$ docker build -t chenrushan/node-web-app .
Sending build context to Docker daemon  19.46kB
Step 1/7 : FROM node:8
 ---> ed145ef978c4
Step 2/7 : WORKDIR /usr/src/app
 ---> Running in 838266182fba
Removing intermediate container 838266182fba
 ---> fbc1b104e41e
Step 3/7 : COPY package*.json ./
 ---> c64ce7038ed4
Step 4/7 : RUN npm install
 ---> Running in dc7bdac0de10
npm WARN docker_web_app@1.0.0 No repository field.
npm WARN docker_web_app@1.0.0 No license field.

added 50 packages in 2.96s
Removing intermediate container dc7bdac0de10
 ---> a49b8a69c3f1
Step 5/7 : COPY . .
 ---> 709a2bff95f6
Step 6/7 : EXPOSE 8080
 ---> Running in 48a49a76d95e
Removing intermediate container 48a49a76d95e
 ---> 3e6dc88e41da
Step 7/7 : CMD [ "npm", "start" ]
 ---> Running in 43fd774af20f
Removing intermediate container 43fd774af20f
 ---> f92590f39d6a
Successfully built f92590f39d6a
Successfully tagged chenrushan/node-web-app:latest
```

用`docker images`就可以看到你构建的对象啦：

```bash
$ docker images

# Example
REPOSITORY                      TAG        ID              CREATED
node                            8          1934b0b038d1    5 days ago
<your username>/node-web-app    latest     d64d3505b0d2    1 minute ago
```

## 运行镜像

使用`-d`运行镜像，会以分离模式(detached mode)运行容器，使容器在后台运行。用`-p`则会将公共端口(public port)重定向到容器内的专用端口(private port)。运行之前构建的镜像：

```bash
$ docker run -p 49160:8080 -d <your username>/node-web-app
```

打印app的输出：

```bash
# Get container ID
$ docker ps

# Print app output
$ docker logs <container id>

# Example
Running on http://localhost:8080
```

我的输出：

```bash
$ docker logs d4fedbda4fb6

> docker_web_app@1.0.0 start /usr/src/app
> node server.js

Running on http://localhost:8080
```

> `docker ps`: 列出容器列表
> 
> `docker logs`: 获取某容器的日志 @

如果你需要进入容器内部，用`exec`命令：

```bash
$ docker exec -it <container id> /bin/bash
```

进入容器后，终端的“前缀”变了，提示我的位置是在 id 为 d4fedbda4fb6 容器的 /usr/src/app 目录下，也就是前面创建的工作目录下。
`$`也变成`#`。

```bash
$ docker exec -it d4fedbda4fb6 /bin/bash

root@d4fedbda4fb6:/usr/src/app#
```

> `docker exec [OPTIONS] CONTAINER COMMAND [ARG...]`
> 
> `--interactive , -i`     让容器的标准输入保持打开
> 
> `--tty , -t`     让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上
> 
> `-it` 为容器打开一个交互式终端 @

试试在终端输入命令：

```bash
root@d4fedbda4fb6:/usr/src/app# ls

Dockerfile  node_modules  package-lock.json  package.json  server.js

root@d4fedbda4fb6:/usr/src/app# pwd

/usr/src/app
```

那怎么退出容器终端呢？通过 `exit` 命令或 `Ctrl+d` 来退出。

```bash
root@d4fedbda4fb6:/usr/src/app# exit

chenrushandeMacBook-Pro:node-app chenrushan$
```

## 测试

获取Docker映射app的端口来测试app：

```bash
$ docker ps

# Example
ID            IMAGE                                COMMAND    ...   PORTS
ecce33b30ebf  <your username>/node-web-app:latest  npm start  ...   49160->8080
```

上面的Example，Docker把容器内部的8080端口映射到计算机上的49160端口。

现在，可以使用`curl`调用app了~（如果没安装`curl`，用这个命令安装：`sudo apt-get install curl`）:

```bash
$ curl -i localhost:49160

HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 12
ETag: W/"c-M6tWOb/Y57lesdjQuHeB1P/qTV0"
Date: Fri, 10 Aug 2018 12:53:20 GMT
Connection: keep-alive

Hello world
```

> Hello world 出来啦~ @

-- 🌰 END --

## 暂停、重新启动、终止、删除容器

### 暂停(pause)和取消暂停(unpaus)容器

用`docker container pause`来暂停容器，暂停后，容器的STATUS会带有`(Paused)`：

```bash
$ docker container pause d4fedbda4fb6

$ docker ps

CONTAINER ID        IMAGE                     COMMAND             CREATED             STATUS                      PORTS                     NAMES
d4fedbda4fb6        chenrushan/node-web-app   "npm start"         About an hour ago   Up About an hour (Paused)   0.0.0.0:49160->8080/tcp   amazing_hopper
```

暂停后怎么取消暂停？用`docker container unpause`：

```bash
$ docker container unpause d4fedbda4fb6

$ docker ps

CONTAINER ID        IMAGE                     COMMAND             CREATED             STATUS              PORTS                     NAMES
d4fedbda4fb6        chenrushan/node-web-app   "npm start"         About an hour ago   Up About an hour    0.0.0.0:49160->8080/tcp   amazing_hopper
```

### 终止(stop)、重新启动(start)终止的容器，重新启动(restart)运行状态的容器

终止容器，容器的STATUS变为`Exited`：

```bash
$ docker container stop d4fedbda4fb6

$ docker container ls -a

CONTAINER ID        IMAGE                     COMMAND             CREATED             STATUS                      PORTS               NAMES
d4fedbda4fb6        chenrushan/node-web-app   "npm start"         About an hour ago   Exited (0) 20 seconds ago                       amazing_hopper
```

处于终止状态的容器，通过 `docker container start` 命令来重新启动：

```bash
$ docker container start d4fedbda4fb6

$ docker ps

CONTAINER ID        IMAGE                     COMMAND             CREATED             STATUS              PORTS                     NAMES
d4fedbda4fb6        chenrushan/node-web-app   "npm start"         About an hour ago   Up 8 seconds        0.0.0.0:49160->8080/tcp   amazing_hopper
```

此外，`docker container restart` 命令会将一个 **运行状态** 的容器终止，然后再重新启动它。

```bash
$ docker container restart d4fedbda4fb6

$ docker ps

CONTAINER ID        IMAGE                     COMMAND             CREATED             STATUS              PORTS                     NAMES
d4fedbda4fb6        chenrushan/node-web-app   "npm start"         About an hour ago   Up 5 seconds        0.0.0.0:49160->8080/tcp   amazing_hopper
```
