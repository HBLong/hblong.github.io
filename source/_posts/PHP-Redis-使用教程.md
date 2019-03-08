---
title: PHP Redis 使用教程
date: 2018-11-07 20:51:54
tags:
  - PHP Redis
  - Redis
categories:
  - PHP
---

日常使用Redis的过程中发现搜到的文档对于使用方法、返回值这些都不是很全，因此写个文档整理在一起方便查找。除了比较难找的，后面会分配时间将常用的也不断补全。

<!-- more -->



## 连接


### connect
使用方法：$redis->connect($host, $port)
返回值：被移除key的数量
参数：**host**服务地址；  **port**端口号

用途：切换redis库
```php
$redis = new Redis();
$redis->connect('127.0.0.1', 6379);
```


### select
使用方法：$redis->select($db_number)
返回值：被移除key的数量
参数：库对应的数值
用途：切换redis库
```php
$redis = new Redis();
$redis->connect('127.0.0.1', 6379);
$redis->select(1); // 切换到1库
```


<br />

## 键（KEY）
### del
使用方法：$redis->del($key)
返回值：被移除key的数量
参数：可以是单个键或者键数组
用途：删除指定键（单个或者多个）

```php
<?php
// 单个删除
$key = 'test';
$redis->del($key); // 返回1，键不存在则返回0

// 批量删除
$key_array = [ 'test1', 'test2', 'test3' ];
$result = $redis->del($key_array); // 返回3
```


### set
使用方法：$redis->set($key, $value)
返回值：true | false
用途：设置键对应的值

### get
使用方法：$redis->get($key)
返回值：键对应的值
用途：获取键对应的值

```php

<?php

$key = 'name';
$redis->set($key, 'xiaoming');
$redis->get($key); // 返回 xiaoming

```


### exists
使用方法：$redis->exists($key)
返回值：true | false
用途：判断键是否存在


### setex
使用方法：$redis->setex('key', 3600, 'value');
用途：设置一小时超时时间

### setnx
使用方法：$redis->setnx('key', 'value');
用途：将key的值设为value，当且仅当key不存在。 若给定的key已经存在，则SETNX不做任何动作。
```
$result = $redis->setnx('key', 'value');
var_dump($result);  // 首次设置键，返回true
$result = $redis->setnx('key', 'value1');
var_dump($result);  // 库内已存在键，返回false
$result = $redis->get('key');
var_dump($result);  // 返回 value
```

<br />

## Hash（哈希表）

### hmset
使用方法：$redis->hmset($key, $value_array);
返回值：true | false
用途：给哈希表批量赋值（键值对）
```
$value_array = [
      'key1' => 'value1',
      'key2' => 'value2',
      'key3' => 'value3',
];
$redis->hmset($key, $value_array); // 设置成功，返回true
```

### hmget
使用方法：$redis->setex($key, $value_array);
用途：批量获取hash表的值，返回键值对，无键为false
```
$value_array = [
      'key1',
      'key4',
      'key5',
];
$result = $redis->hmget($key, $value_array);
var_dump($result);

===== output =====
array(3) {
  ["key1"]=>
  string(6) "value1"
  ["key4"]=>
  bool(false)   // 未设置键，返回false
  ["key5"]=>
  bool(false)   // 未设置键，返回false
}
```


<br />

## 参考文档
[redis中文手册](http://redisdoc.com)
[PHP-redis中文文档](https://www.cnblogs.com/weafer/archive/2011/09/21/2184059.html)
[phpredis中文手册——《redis中文手册》 php版](https://www.cnblogs.com/weafer/archive/2011/09/21/2184059.html)