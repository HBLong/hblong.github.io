---
title: hexo博客搭建教程
date: 2018-11-08 10:04:36
tags: 
  - hexo
---

这篇博文介绍了我在搭建Hexo+Next主题时遇到的各种需要处理的问题。博客使用过程中也会继续不断完善。

<!-- more -->


## 环境
```
$ hexo -v
hexo: 3.8.0
hexo-cli: 1.1.0
os: Windows_NT 10.0.17134 win32 x64
node: 8.9.3
http_parser: 2.7.0
v8: 6.1.534.48
uv: 1.15.0
zlib: 1.2.11
ares: 1.10.1-DEV
modules: 57
nghttp2: 1.25.0
openssl: 1.0.2n
icu: 59.1
unicode: 9.0
cldr: 31.0.1
tz: 2017b
```

<br />

## 评论
[第三方集成服务-来必力](http://theme-next.iissnan.com/third-party-services.html#livere)
Next主题自带，这里我使用的是 [来必力](https://www.livere.com/)，注册后获取 LiveRe UID，直接改主题配置文件
```
livere_uid: #your livere_uid
```
如果无法注册请科学上网，注册时会显示一堆韩文，大致意思是去邮箱看验证码
注册完登录选择 city 服务，然后进入[授权码页](https://www.livere.com/insight/myCode)复制以下 data-uid
```
<!-- 来必力City版安装代码 -->
<div id="lv-container" data-id="city" data-uid="********">
```

<br />

## 统计
[第三方集成服务-不蒜子统计](http://theme-next.iissnan.com/third-party-services.html#analytics-busuanzi)
[不蒜子统计官网](http://busuanzi.ibruce.info/)
Next主题自带，这里我使用的是 不蒜子统计，直接改主题配置文件
```
busuanzi_count:
  enable: true
```

### 统计显示异常
deploy博客后发现统计显示异常，调试后发现不蒜子的JS 404 加载不出来了。到官网看了一下，原来旧域名过期。


#### 解决方法
1）找到NexT下引用不蒜子统计的文件。路径为/theme/next/layout/_third-party/analytics/busuanzi-counter.swig
2）替换js链接
```
将

<div class="busuanzi-count">
  <script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>

替换为

<div class="busuanzi-count">
  <script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```


<br />

## 搜索
Next主题自带有hexo-generator-search插件
直接在主题配置文件themes/next/_config.yml下修改配置即可
```
local_search:
  enable: true
```

<br />


## 图片资源文件引入
### source 资源文件夹（类似于全局）
对于少量或者公用的文件，可以存放在source资源文件夹
例如 source/images/test.jpg
通过 `![](/images/test.jpg)` 来访问它们
![source/images/test.jpg](/images/test.jpg)


<br />

### 文章资源文件夹（按文章分类）

#### 修改配置文件 _config.yml
```
post_asset_folder: true

```
资源文件管理功能打开后，每次创建新文章都会自动创建一个资源文件夹。



#### 相对路径引用
例如 _posts/post_name/img/test.jpg
通过 `![](./img/test.jpg)` 来访问它们
![./img/test.jpg](./img/test.jpg)




<br />

### CDN引用
除了在本地存储图片，还可以将图片上传到一些免费的CDN服务中。将生成的地址直接拿来引用即可。
`![](https://nodejs.org/static/images/logo.svg)`
![CDN引用](https://nodejs.org/static/images/logo.svg)




<br />

## 没有标签页、分类页
### 没有标签页，tags 404
每篇博文都可以设置对应的标签
```
---
title: hexo博客搭建教程
date: 2018-11-08 10:04:36
tags: hexo
---
```

调用以下命令生成标签页
$ hexo new page tags

编辑主题配置文件，将tags添加到目录menu
```
menu:
  home: /
  archives: /archives
  tags: /tags
```

#### 使用多个标签
标签的设置可以使用[YAML语法](http://zh.wikipedia.org/zh-hant/YAML)
```shell
---
title: hexo博客搭建教程
date: 2018-11-08 10:04:36
tags: 
    - 标签1
    - 标签2
---
```
    


<br />

### 没有分类页，categories 404

给文章设置分类
```
categories:
  - 一级分类
  - 二级分类
  - 三级分类
  - ...
```

调用以下命令生成分类页
$ hexo new page categories

编辑主题配置文件，将tags添加到目录menu
```
menu:
  home: /
  categories: /categories/
  archives: /archives/
  tags: /tags/
```

#### 使用多级分类

使用[YAML语法](http://zh.wikipedia.org/zh-hant/YAML)
```shell
---
title: hexo博客搭建教程
date: 2018-11-08 10:04:36
categories:
  - 一级分类
  - 二级分类
  - 三级分类
  - ...
---
```


<br />

## 首页只显示文章摘要

### 自动截取摘要

在主题配置文件设置 `enable:true`
```
# Automatically Excerpt. Not recommend.
# Please use <!-- more --> in the post to control excerpt accurately.
auto_excerpt:
  enable: false
  length: 150
```

但是这样子设置后，截取的摘要是纯文本，会很难看


### 手动设置摘要

可以在每篇文章内使用标签`<!-- more -->`来手动设置
```
---
title: hexo博客搭建教程
date: 2018-11-08 10:04:36
tags: 
  - hexo
---

摘要内容

<!-- more -->

正文

```