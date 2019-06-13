---
title: "如何使用curl进行http请求"
date: 2016-09-01
categories: 工具
tags: ["工具"]
---

如今的互联网时代，我们在日常开发中经常需要测试HTTP[S] API，有一些含用户界面的工具使用很方便，如Chrome插件Postman等。 但作为码农的我们有时候会觉得带图形的东西还不如命令行下敲一段命令来的简便，今天介绍一款可以在命令行下测试API的工具curl。

<!--more-->
### curl是什么？
curl是一个利用URL语法在命令行下工作的文件传输工具，1997年首次发行。它支持文件上传和下载，所以是综合传输工具，但按传统，习惯称cURL为下载工具。cURL还包含了用于程序开发的libcurl。
curl支持的通信协议有FTP、FTPS、HTTP、HTTPS、TFTP、SFTP、Gopher、SCP、Telnet、DICT、FILE、LDAP、LDAPS、IMAP、POP3、SMTP和RTSP。(来自wikipedia)

### HTTP 请求
#### 1.POST请求
```
curl -H "Content-Type: application/json" -X POST -d \
  '{
    "key1": "value1",
    "key2": "value2",
    ......
  }' http://localhost:3000/
```
> 参数说明：    
-H: 表示后面紧跟请求头部参数
-X: 后面紧跟请求指令，如:GET/POST/DELETE/PUT/UPDATE等，没有此参数时默认是GET请求。
-d: 后紧跟HTTP POST 数据。

#### 2. GET请求
```
curl -X GET http://localhost:3000/
```
或者
```
curl http://localhost:3000/
```
NOTE:可用此命令查看网页源码，如：

```
curl www.google.com
```

#### 3. DELETE请求
```
curl -X DELETE http://localhost:3000/
```