Zebra-Crontab 基于PHP的定时任务管理器
=============
[![Total Downloads](https://img.shields.io/packagist/dt/jenner/crontab.svg?style=flat)](https://packagist.org/packages/jenner/crontab)
[![Latest Stable Version](http://img.shields.io/packagist/v/jenner/crontab.svg?style=flat)](https://packagist.org/packages/jenner/crontab)
[![License](https://img.shields.io/packagist/l/jenner/crontab.svg?style=flat)](https://packagist.org/packages/jenner/crontab)
为什么使用PHP管理crontab
------------
一般在定时任务较少的情况下，使用原生的crontab服务一般不会有什么问题，但当定时任务较多时就会产生如下问题：
+ 文本形式的定时任务可读性很差，在没有任何注释的情况下，新人很难在不读源码的情况下了解定时任务的业务逻辑
+ 在分布式的场景中，定时任务会散落到多台机器上，无法统一管理
+ 定时任务的日志不能集中化管理，对定时任务的运行分析及故障排除比较麻烦
+ 不同用户下的定时任务需要分开编写，可读性、可维护性差
基于以上几点原因，我们迫切的需要一个可以集中化管理的、可配置的定时任务管理器
但自己开发一套分布式的定时任务系统何其复杂，所以作者采用crontab服务做辅助，使用php实现对定时任务的配置管理

使用php管理定时任务有哪些优势
-----------
+ 定时任务可以不再是以文本方式的形式存在，可以存储在缓冲、数据库中，甚至你可以开发管理功能，在后台对定时任务进行编辑
+ 定时任务的日志是可配置的，你可以按照业务需求，对日志进行差异化配置
+ crontab服务在不同用户下的定时任务需要分开编写，使用phpCrontab管理后，可以集中配置脚本执行权限

使用方式如下：
+ 编写一个任务管理器，可参考test/simple.php
+ 将上述脚本添加到crontab中，一分钟执行一次

特性
-----------
+ 采用多进程，一个任务为一个进程，解决串行执行延迟问题
+ 支持设定用户、用户组
+ 支持设定多个执行时间
+ 支持输出重定向

你可以通过如下方式实现分布式定时任务管理
------------
+ 定时任务配置信息写入redis单点或集群，使用订阅机制自动分发
+ 定时任务写入文件，修改时rsync同步
+ 写入mysql数据库（推荐主从架构），自动同步


**示例：**
```php
<?php
$crontab_config = [
    'test_1' => [
        'name' => '服务监控1', //任务名称
        'cmd' => 'php -v', //需要执行的cli命令
        'output' => '/tmp/test.log', //输出重定向文件
        'time' => '* * * * *', //定时任务时间配置，与crontab抑制
        'user_name' => 'www', //cli命令运行的用户身份
        'group_name' => 'group_name', //cli命令运行的用户组归属
    ],
    'single_test' => [
        'name' => 'php -i',
        'cmd' => 'php -i',
        'output' => '/tmp/single_script.log',
        'time' => [
            '* * * * *',
            '* * * * *',
        ],
    ],
];

$crontab_server = new \Jenner\Zebra\Crontab\Crontab($crontab_config);
$crontab_server->start();
```

工具短小，但很精悍
-----------
在分布式场景中，你可以把定时任务写入数据库中进行统一管理，你可以设定哪些定时任务是由哪些机器执行，
然后通过生成文本文件的方式发送到所有机器上，再由这些机器上的phpCrontab读取处理；从而实现分布式场景下的定时任务统一管理。


[博客地址:www.huyanping.cn](http://www.huyanping.cn/ "程序猿始终不够")



