---
layout: post
title: "用 NodeMailer 批量发邮件"
subtitle: "Send emails by Nodemailer"
author: "Rushan"
header-img: "img/post-bg-default-blue.jpg"
header-img-credit: "Photo by Matteo Fusco on Unsplash"
header-img-credit-href: "https://unsplash.com/photos/m94kn8Rp61Q"
header-mask: 0.4
tags:
  - JavaScript
  - Nodemailer
---

[批量发邮件 — JavaScript社区](https://xugaoyang.com/post/J5CvpNK9Zi)

第三方库：[NodeMailer — Github](https://github.com/nodemailer/nodemailer)

- [SMTP transport](https://nodemailer.com/smtp/)
- [Verify SMTP connection configuration](https://nodemailer.com/smtp/#verify-smtp-connection-configuration) 验证邮箱SMTP的连接配置是否正常
- [Sending mail](https://nodemailer.com/usage/#sending-mail)
- [Address object](https://nodemailer.com/message/addresses/) 地址加上姓名；添加多个地址

## 安装

```bash
npm i nodemailer
npm i nodemailer-smtp-transport
```

## 第一版

```js
// 引入
const mailer = require('nodemailer');
const smtpTransport = require('nodemailer-smtp-transport');

// 补充author代码，发送者邮箱配置
const author = {
    host: 'smtp.163.com',
    port: 465,
    secure: true, // 建议用true,采用SSL协议，上面对应SSL协议端口号。在邮箱设置SMTP时可以看到相关信息。
    auth: {
      user: 'mymail@163.com', // 如果用gmail发送，要查看文档`Using Gmail`那一节
      pass: 'mykey' // 授权码
    }
};

const transporter = mailer.createTransport(smtpTransport(author));

// 补充mailOptions代码，即邮件信息
let mailOptions = {
	from: '陈如珊 <mymail@163.com>', // 可以参考第3节的Address object
	to: 'mymail@qq.com, mymail@gmail.com',
	subject: '回答:【10.24】批量发邮件 | Nodemailer',
	text: 'Hello, JS',
	html:'<b>Hello, JS</b>'
};

transporter.sendMail(mailOptions, (error, info) => {
	if (error) {
		return console.log(error);
	} else {
	console.log('Message sent: %s', info.messageId);
	}
});
```

## 第二版

看到家树同学还有async版本，我想起了看文档时，有提到`promise`，比如[sending mail](https://nodemailer.com/usage/#sending-mail)、[Verify SMTP connection configuration](https://nodemailer.com/smtp/#verify-smtp-connection-configuration)。

还在GitHub翻到一个example：[await.js](https://github.com/nodemailer/nodemailer/blob/master/examples/await.js)

可以试一试~

然后就有了第二版：

```js
const mailer = require('nodemailer');
const smtpTransport = require('nodemailer-smtp-transport');

const author = {
	host: 'smtp.163.com',
	port: 465,
	secure: true,
	auth: {
	  user: 'mymail@163.com',
	  pass: 'mykey'
	}
};

let message = {
	from: '陈如珊 <mymail@163.com>',
	to: 'mymail@qq.com, mymail@gmail.com',
	subject: '回答:【10.24】批量发邮件 | Nodemailer',
	text: 'Hello, JS',
	html:'<b>Hello, JS</b>'
};

async function sendMails (author, message) {
	try{
		const transporter = mailer.createTransport(smtpTransport(author));

		// verify SMTP configuration
		const verify = await transporter.verify();
		console.log('Server is ready to take our messages');

		// send mail
		const info = await transporter.sendMail(message);
		console.log('Message sent successfully!');

	} catch (err) {
	console.log(err.message);
	}
}

sendMails(author, message);
```

经测试，可行。