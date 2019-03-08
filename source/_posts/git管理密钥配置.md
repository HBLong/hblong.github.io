---
title: git管理密钥配置
date: 2018-11-03 18:06:26
tags:
---

这篇博文介绍了如何配置git密钥，并在github使用git密钥来进行远程连接鉴权。

<!-- more -->

### git下载
[下载地址](https://git-scm.com/download/win)

### git配置
```shell
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub注册邮箱"
```

### 生成密钥
```shell
ssh-keygen -t rsa -b 4096 -C "你的GitHub注册邮箱"

Enter file in which to save the key          // 密钥保存位置，回车默认
Enter passphrase (empty for no passphrase):  // 输入密码，直接回车
Enter same passphrase again:                 // 输入确认密码，直接回车
```

### github密钥管理
[github密钥管理直达链接](https://github.com/settings/keys)
1）New SSH Key，复制本地 ~/.ssh（window下为 C:\User\用户\.ssh）的 id_rsa.pub
2）Title为标题，任意填即可，将刚刚复制的id_rsa.pub内容粘贴进去
3）最后点击Add SSH key。


### 检验
在Git Bash中检测GitHub公钥设置是否成功，输入
`ssh -T git@github.com`

<br />

### 为不同网站应用各自的 SSH KEY
编辑ssh配置 `vim ~/.ssh/config`
追加以下内容
```
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_rsa_a

Host git.oschina.net
    HostName git.oschina.net
    User git
    IdentityFile ~/.ssh/id_rsa_b
```