---
title: nginx配置总结
date: 2018-12-03 19:30:26
tags:
    - nginx
---

平时使用nginx的过程发现有很多零碎的东西在网上很难查看，因此总结归纳一下，主要列举了nginx常用的变量和围绕一些知识点展示用例。
<!-- more -->

### nginx 全局变量
请求http报文如下
```
Request URL: http://www.test1.com/?test_var=123&test_var2=456
Request Method: POST
Status Code: 200 OK
Remote Address: 127.0.0.1:80
Referrer Policy: no-referrer-when-downgrade
Accept: */*
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Origin: http://www.test.com
Referer: http://www.test.com/test.html
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36

post_test_var=789&POst_test_var1=101112
```

nginx全局变量列举：

| 变量 | 值 | 说明 |
| ------ | ------ | ------ |
| $remote_addr|127.0.0.1|客户端IP地址；
| $http_origin|http://www.test.com|HOST；
| $http_referer|http://www.test.com/test.html|origin源；
| $http_host|www.test1.com|Referer；
| $args | test_var=123&test_var2=456 | #这个变量等于请求行中的参数，同$query_string
| $content_length | 39 | 请求头中的Content-length字段。
| $content_type | application/x-www-form-urlencoded; charset=UTF-8 | 请求头中的Content-Type字段。
| $document_root | D:/phpStudy/WWW | 当前请求在root指令中指定的值。
| $host | www.test1.com | 请求主机头字段，否则为服务器名称。
| $http_user_agent | http_user_agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.| 客户端agent信息
| $http_cookie |  | 客户端cookie信息
| $limit_rate | 0 | 这个变量可以限制连接速率。
| $request_method | POST | 客户端请求的动作，通常为GET或POST。
| $remote_addr | 127.0.0.1 | 客户端的IP地址。
| $remote_port | 65002 | 客户端的端口。
| $remote_user |  | 已经经过Auth Basic Module验证的用户名。
| $request_filename | D:/phpStudy/WWW/ | 当前请求的文件路径，由root或alias指令与URI请求生成。
| $scheme | http | HTTP方法（如http，https）。
| $server_protocol | HTTP/1.1 | 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
| $server_addr | 127.0.0.1 | 服务器地址，在完成一次系统调用后可以确定这个值。
| $server_name | www.test1.com | 服务器名称。
| $server_port | 80 | 请求到达服务器的端口号。
| $request_uri | /?test_var=123&test_var2=456 | 包含请求参数的原始URI，不包含主机名，如 ”/foo/bar.php?arg=baz”。
| $uri | / | 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
| $document_uri | / | 与$uri相同。