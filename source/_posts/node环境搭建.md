---
title: node环境搭建
date: 2018-11-08 09:44:42
tags:
---


这篇博文介绍了各种搭建node方法以及加速方案。其中极力推荐使用n进行版本管理，特别方便。而且在国内推荐使用cnpm来加速依赖安装，特别爽。

<!-- more -->

## node环境搭建
### 下载安装
#### 通过源码编译
[node下载地址](https://nodejs.org/zh-cn/download/)
```shell
$ wget https://nodejs.org/dist/v10.13.0/node-v10.13.0.tar.gz
$ tar -zxvf node-v10.13.0.tar.gz
$ cd node-v10.13.0
$ make && make install
```


#### 通过安装包安装
根据系统环境以及选择安装包[node下载地址](https://nodejs.org/zh-cn/download/)


#### 通过yum（CentOS）

```shell
$ yum install -y nodejs
```

<br />

## 版本切换（通过npm）
```shell
$ npm i -g n
$ n 10         // 切换到v10
```


## npm加速（cnpm）
```shell
$ npm i -g cnpm
$ cnpm i          // 使用cnpm 代表 npm命令
```

## 项目常规编译流程
```shell
$ cd 项目目录
$ npm install
$ npm run build
```