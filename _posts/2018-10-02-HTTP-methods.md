---
layout: post
title: "HTTP 请求方法"
subtitle: "HTTP Request methods"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - HTTP
---

HTTP 请求分为三个部分：状态行、请求头、消息主体。

```
<method> <request-URL> <version>
<headers>

<entity-body>
```

其中 method 有以下几种：

- GET
- HEAD
- POST
- PUT
- PATCH
- DELETE
- CONNECT
- OPTIONS
- TRACE

## GET

GET 方法，用于获取数据，请求指定资源。

```http
GET / HTTP/1.1
Host: developer.mozilla.org
Accept-Language: en-US
```

## HEAD

HEAD 方法与 GET 方法相同，但响应时只有 headers，没有 body。比如，为了节省带宽，在决定下载大型资源之前，可以先发送 HEAD 请求。

```http
HEAD / HTTP/1.1
Host: developer.mozilla.org
Accept-Language: en
```

## POST

POST 方法将数据发送到服务器。请求 body 的类型由`Content-Type` header 指定，比如：

- `application/json`
- `application/x-www-form-urlencoded`
- `multipart/form-data`
- `text/xml`

POST 方法不是幂等的，若反复执行多次对应的每一次都会创建一个新资源。

```http
POST / HTTP/1.1
Host: hello.com
Content-Type: application/json;charset=utf-8

{"title":"hello world","content":"HTTP POST request"}
```

## PUT

PUT 方法，创建新资源，或者用请求中的有效 playload 替换目标资源。

与 POST 方法的区别在于，PUT 方法是幂等的，调用一次跟连续调用多次是等价的；而连续调用多次 POST 方法肯会有副作用，比如，一个订单重复提交多次。

```http
PUT /new.thml HTTP/1.1
Host: example.com
Content-type: application/json;charset=utf-8

{"userID":"c5s6g3","userName":"John","userGender":"male","userEmail":"John@gmail.com"}
```

### 响应

如果目标资源不存在，PUT 方法新建了一份，那么必须响应 201 来通知客户端。

```http
HTTP/1.1 201 Created
Content-Location: /new.html
```

如果目标资源已存在，依照 PUT 方法进行了更新，那么必须返回 200（OK）或者 204（No Content）来表示请求成功完成。

```http
HTTP/1.1 204 No Content
Content-Location: /existing.html
```

## PATCH

PATCH 方法请求资源的部分修改。

与 POST 方法相似，PATCH 是非幂等的，连续多个的相同请求会产生不同的效果。

```http
PATCH /new.thml HTTP/1.1
Host: example.com
Content-type: application/json;charset=utf-8
IF-Match:"c5s6g3"

{"userEmail":"John.Bill@gmail.com"}
```

### 响应

成功的响应用 204 响应代码表示，因为示例中的响应不携带 body（200 则含有 body）。当然，其他成功响应码也可以。

```http
HTTP/1.1 204 No Content
Content-Location: /John.json
ETag: "c5s6g4"
```

## DELETE

DELETE 方法，删除指定资源。

```http
DELETE /file.html HTTP/1.1
```

如果成功删除，有几种响应可能：

- 202 (Accepted) 如果请求已接收，但还没执行。
- 204 (No Content) 如果请求已完成，且没有更多信息要返回。
- 200 (OK) 如果请求已完成，且响应信息包含了相关描述。

```http
HTTP/1.1 200 OK
Date: Sun, 21 Oct 2018 16:28:00 GMT

<html>
  <body>
    <h1>File deleted.</h1>
  </body>
</html>
```

## CONNECT

CONNECT方法建立一个到服务器的隧道（tunnel），由目标资源标识。

```http
CONNECT server.hello.com:80 HTTP/1.1
Host: server.hello.com:80
Proxy-Authorization: basic aGVsbG86d29ybGQ=
```

## OPTIONS

OPTIONS 方法，用于获取目的资源所支持的通信选项。客户端可以对特定的 URL 使用 OPTIONS 方法，也可以对整站（将 URL 设置为“*”）使用该方法。

CORS 中的预检请求：

预检请求报文中，`Origin` 表示请求的来源；`Access-Control-Request-Method` 首部字段告知服务器实际请求所使用的 HTTP 方法；`Access-Control-Request-Headers` 首部字段告知服务器实际请求所携带的自定义首部字段。服务器基于从预检请求获得的信息来判断，是否接受接下来的实际请求。

```http
OPTIONS /cors HTTP/1.1
Origin: http://api.bob.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: X-Custom-Header
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```

服务器收到"预检"请求以后，检查了 `Origin`、`Access-Control-Request-Method` 和 `Access-Control-Request-Headers` 字段以后，确认允许跨源请求，就可以做出回应。

```http
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
Content-Type: text/html; charset=utf-8
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```

服务器所返回的`Access-Control-Allow-Origin` 字段，表示`http://api.bob.com` 可以请求数据，该字段也可以设为星号 `*`，表示同意任意跨源请求。；`Access-Control-Allow-Methods` 字段将所有允许的请求方法告知客户端。

## TRACE

TRACE f方法，沿着目标资源的路径指向消息回环（loop-back）测试，提供实用的调试机制。

请求的最终接受者，应该将接收到的信息反映给客户端，信息作为 `Content-Type` 为 `message/http` 的 200（OK）响应的 body。最终收件人是原始服务器，或者第一个接收 `Max-Forwards` 为0的请求的服务器。