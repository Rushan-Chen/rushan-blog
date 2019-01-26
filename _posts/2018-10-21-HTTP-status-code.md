---
layout: post
title: "HTTP响应状态码"
subtitle: "HTTP status code"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - HTTP
---

- `1××` Informational 信息响应
  - `100 Continue`
    - 临时响应，客户端应继续请求，若已完成，则忽略
  - `101 Switching Protocols`
    - 响应客户端的`Upgrade`请求，指明服务端要切换的目标协议
  - `102 Processing`
    - 服务端已收到请求并在处理中，暂时无响应

---

- `2××` Success 成功响应
  - `200 OK`
    - 请求成功
  - `201 Created`
    - 请求成功，且新资源已创建。一般是对 POST 请求或者一些 PUT 请求的响应。
  - `202 Accepted`
    - 请求已被接收，但没有采取行动。适用于其他进程或服务器处理请求或批处理的情况。
  - `203 Non-authoritative Information`
    - 返回的元信息集合，不是源服务端上的确切集合，而是来自本地或第三方的副本。除了这种情况，响应 200 OK 才更合适。
  - `204 No Content`
    - 响应不包含任何内容，但 headers 可能会有用。user-agent 或许可以用它更新它缓存的 headers。
  - `205 Reset Content`
    - 在完成请求之后发送此响应代码，告知发送请求的 user agent 重置文档视图。
  - `206 Partial Content`
    - 服务器已成功处理了部分 GET 请求。请求必须包含一个 Range header，表示所需的范围，并且可能包含一个 If-Range header 作为请求条件。(迅雷这类的 HTTP 下载工具都是使用此类响应实现断点续传或者将一个大文档分解为多个下载段同时下载。)

---

- `3××` Redirection 重定向
  - `300 Multiple Choices`
    - 该请求有多个可能的响应。用户代理或用户应选择其中一个。没有标准化的方法来选择其中一个答案。
  - `301 Moved Permanently`
    - 此响应代码表示所请求资源的 URI 已更改。将来对此资源的请求都应该使用本响应返回的若干 URI 之一。除非额外指定，否则此响应可缓存。
  - `302 Found`
    - 请求的资源现在临时从不同的 URI 响应请求。由于重定向是临时的，以后客户端应当继续向原地址发送请求。只有在 Cache-Control 或 Expires 中进行了设定，此响应才可缓存。
  - `303 See Other`
    - 对应当前请求的响应可以在另一个 URI 上被找到，而且客户端应当采用 GET 的方式访问那个资源。这个方法的存在主要是为了允许由脚本激活的 POST 请求输出重定向到一个新的资源。
  - `304 Not Modified`
    - 如果客户端发送了一个带条件的 GET 请求且该请求已被允许，而文档的内容（自上次访问以来或者根据请求的条件）并没有改变，则服务器应当返回这个状态码。304 响应禁止包含消息体，因此始终以 header 后的第一个空行结尾。
  - `307 Temporary Redirect`
    - 请求的资源现在临时从不同的URI 响应请求。由于这样的重定向是临时的，客户端应当继续向原地址发送请求。只有在 Cache-Control 或Expires 中进行了指定的情况下，这个响应才是可缓存的。
  - `308 Permanent Redirect`
    - 这意味着资源现在永久位于由 Location: HTTP Response 标头指定的另一个 URI。 这与 301 Moved Permanently HTTP 响应代码具有相同的语义，但用户代理不能更改所使用的 HTTP 方法：如果在第一个请求中使用 POST，则必须在第二个请求中使用 POST。

---

- `4××` Client Error 客户端错误响应
  - `400 Bad Request`
    - 由于语法格式错误，服务器无法理解请求。客户端不应该在没有修改的情况下重复请求。
  - `401 Unauthorized`
    - 当前请求需要用户验证。该响应必须包含一个适用于被请求资源的 WWW-Authenticate header 用以询问用户信息。客户端可以重复提交一个包含恰当的 Authorization header的请求。如果当前请求已经包含了 Authorization 证书，那么401响应代表着服务器验证已经拒绝了那些证书。
  - `402 Payment Required`
    - 此响应码保留以便将来使用，创造此响应码的最初目的是用于数字支付系统，然而现在并未使用。
  - `403 Forbidden`
    - 服务器已经理解请求，但是拒绝执行它。与 401 响应不同的是，身份验证并不能提供任何帮助，而且这个请求也不应该被重复提交。如果这不是一个 HEAD 请求，而且服务器希望能够讲清楚为何请求不能被执行，那么就应该在实体内描述拒绝的原因。
  - `404 Not Found`
    - 请求失败，请求所希望得到的资源未被在服务器上发现。没有信息能够告诉用户这个状况到底是暂时的还是永久的。假如服务器知道情况的话，应当使用410状态码来告知旧资源因为某些内部的配置机制问题，已经永久的不可用，而且没有任何可以跳转的地址。
  - `405 Method Not Allowed`
    - 请求行中指定的请求方法不能被用于请求相应的资源。该响应必须返回一个Allow 头信息用以表示出当前资源能够接受的请求方法的列表。鉴于 PUT，DELETE 方法会对服务器上的资源进行写操作，因而绝大部分的网页服务器都不支持或者在默认配置下不允许上述请求方法，对于此类请求均会返回405错误。
  - `406 Not Acceptable`
    - 请求的资源的内容特性无法满足请求头中的条件，因而无法生成响应实体。
  - `407 Proxy Authentication Required`
    - 此代码类似于401（未授权），但表示客户端必须首先使用代理进行身份验证 。代理必须返回一个 Proxy-Authenticate 头字段，其中包含适用于所请求资源的代理的质询。客户端可以使用合适的 Proxy-Authorization 头字段重复该请求。
  - `408 Request Timeout`
    - 请求超时。客户端在服务器准备等待的时间内没有产生请求。客户端可以在以后不经修改的情况下重复请求。
  - `409 Conflict`
    - 由于与资源的当前状态冲突，无法完成请求。
  - `410 Gone`
    - 请求的资源在服务器上不再可用，而且没有任何已知的转发地址。这样的状况应当被认为是永久性的。如果服务器不知道或者无法确定这个状况是否是永久的，那么就应该使用 404 状态码。
  - `411 Length Required`
    - 服务器拒绝在没有定义 Content-Length 头的情况下接受请求。在添加了表明请求消息体长度的有效 Content-Length 头之后，客户端可以再次提交该请求。
  - `412 Precondition Failed`
    - 服务器在验证在请求的头字段中给出先决条件时，没能满足其中的一个或多个。
  - `413 Payload Too Large`
    - 服务器拒绝处理当前请求，因为该请求提交的实体数据大小超过了服务器愿意或者能够处理的范围。
  - `414 Request-URI Too Long`
    - 请求的URI 长度超过了服务器能够解释的长度，因此服务器拒绝对该请求提供服务。这比较少见，通常的情况包括：本应使用POST方法的表单提交变成了GET方法，导致查询字符串（Query String）过长。
  - `415 Unsupported Media Type`
    - 对于当前请求的方法和所请求的资源，请求中提交的实体并不是服务器中所支持的格式。
  - `416 Requested Range Not Satisfiable`
    - 如果请求中包含了 Range 请求头，并且 Range 中指定的任何数据范围都与当前资源的可用范围不重合，同时请求中又没有定义 If-Range 请求头，那么服务器就应当返回416状态码。
  - `417 Expectation Failed`
    - 服务器无法满足 Expect 请求标头字段中给出的 expectation。

---

- `5××` Server Error 服务端错误响应
  - `500 Internal Server Error`
    - 服务器遇到意外情况，无法满足请求。
  - `501 Not Implemented`
    - 服务器不支持完成请求所需的功能。当服务器无法识别请求方法并且无法为任何资源支持时，会返回此响应。
  - `502 Bad Gateway`
    - 服务器在充当网关或代理时，尝试完成请求时，但从其访问的上游服务器收到无效响应。
  - `503 Service Unavailable`
    - 由于服务器的临时过载或维护，服务器当前无法处理请求。这意味着这是一个暂时的条件，经过一段时间的延迟后会得到缓解。（有些服务器可能是想简单地拒绝连接，不一定是过载）
  - `504 Gateway Timeout`
    - 当服务器作为网关，不能及时得到响应时返回此错误代码。
  - `505 HTTP Version Not Supported`
    - 服务器不支持请求中所使用的HTTP协议版本。

## 参考

- [HTTP/1.1 Status Code Definitions — W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
- [HTTP Status — MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
