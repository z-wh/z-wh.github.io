---
layout: post
title:  "xdebug、浏览器xdebug help插件和sublime搭建PHP调试"
date:   2018-07-13
excerpt: "xdebug、浏览器xdebug插件和sublime搭建PHP调试"
tag:
- scss
- css
---

### xdebug插件PHPini配置
 1. `xdebug.remote_autostart=on` 开启远程调试自动启动,如果设置为on，则会忽略浏览器中是选择Debug还是Disable，都会进行调试。也就是说如果要配合浏览器Xdebug help插件，进行选定页面调试，就要设置为`off`,这样就不用代码从头开始调试，而可以指定从某个有问题的页面开始调试。xdebug help浏览器插件只有chrome 和 firefox浏览器有。其它浏览器要想也能和xdebug通信，因为无插件能开启、关闭调试，所以要把远程自动调试设置为开启。

2. `xdebug.auto_trace = On` 开启自动跟踪,如果设置为on,就能自动进行路径追踪获得更多信息

3. php.ini文件中的配置如下：
```
    [xdebug]
    zend_extension ="e:/wamp64/bin/php/php5.6.35/zend_ext/php_xdebug-2.5.5-5.6-vc11-x86_64.dll";php加载xdebug
    xdebug.remote_enable=on
    xdebug.profiler_enable = on
    xdebug.profiler_output_name = cachegrind.out.%t.%p
    xdebug.profiler_output_dir ="e:/wamp64/xdebug";xdebug 的数据文件目录
    xdebug.trace_output_dir="e:/wamp64/xdebug";xdebug 的数据文件目录
    xdebug.remote_autostart=on;开启远程调试自动启动
    xdebug.remote_handler=dbgp;用于zend studio远程调试的应用层通信协议
    xdebug.remote_host=127.0.0.1;允许连接的zend studio的IP地址
    xdebug.remote_port=9000;反向连接zend studio使用的端口
    xdebug.auto_trace = On ;开启自动跟踪
    xdebug.show_exception_trace = On ;开启异常跟踪
    xdebug.show_local_vars=0
    xdebug.collect_vars = On ;收集变量
    xdebug.collect_return = On ;收集返回值
    xdebug.collect_params = On ;收集参数
    xdebugbug.max_nesting_level = 10000 ;如果设得太小,函数中有递归调用自身次数太多时会报超过最大嵌套数错
```

### Sublime Text3 中的配置

1. 安装 xdebug client

2. 配置如下，记录下，以防忘记：
```
    {
    //本地调试就写这个
    "url": "localhost",

    //本地调试就写这个
    "host": "localhost",

    "port": 9000,

    // 最大显示条数，如果发现context中的数据省略号显示不出来，就把这个数字改大
    "max_children": 50,

    // 数组显示的层级，如果有二维数组、三维数组甚至更多层级的数组想展开显示出来，改这个数字
    "max_depth": 2,
    }
```


