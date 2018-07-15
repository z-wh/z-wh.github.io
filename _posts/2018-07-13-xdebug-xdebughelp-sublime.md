---
layout: post
title:  "xdebug、浏览器xdebug help插件和sublime搭建PHP调试"
date:   2018-07-13
excerpt: "xdebug、浏览器xdebug插件和sublime搭建PHP调试"
tag:
- scss
- css
---

## xdebug插件PHPini配置
1. `xdebug.remote_autostart=on`  

   开启远程调试自动启动,如果设置为on，则会忽略浏览器中是选择Debug还是Disable，都会进行调试。也就是说如果要配合浏览器Xdebug help插件，进行选定页面调试，就要设置为`off`,这样就可以手动控制是否开启页面的xdebug调试，可以指定从某个有问题的页面开始调试。xdebug help浏览器插件只有chrome 和 firefox应用商店有，但chrome应用商店已被墙。其它浏览器要想也能和xdebug通信，因为无插件能开启、关闭调试，所以要把远程自动调试设置为开启。
    * 360浏览器（安全和极速都可以）、chrome等其他浏览器要安装xdebug help插件，也可以通过手动安装的方式安装插件。先从github上找到该[插件主页](https://github.com/mac-cain13/xdebug-helper-for-chrome)，然后下载解压。打开浏览器*插件管理里*的“开发者模式”->"加载已解压的扩展程序"->"确认"，即可安装好。

2. `xdebug.auto_trace = On`  
   开启自动跟踪回溯,如果设置为on,就能自动进行路径追踪获得回溯信息

3. 请求参数触发  
   如果配置xdebug.auto_trace=on的话,你运行任何PHP文件都会产生回溯记录

   但有时候你只需要回溯某一个地址的运行轨迹,可以通过设置xdebug.trace_enable_trigger=on来实现,但前提是要设置xdebug.auto_trace=off(或者删除这个选项)

      配置样本:
          ```
                 xdebug.auto_trace=off
                 xdebug.trace_enable_trigger=on
                 xdebug.trace_output_dir="E:\xdebug"
          ```
   这样你访问/index.php不会产生回溯记录,但是你如果修改一下URL参数,加上XDEBUG_TRACE这个参数名,不用参数值,只要有参数名就行了

   就是访问这样的/index.php?XDEBUG_TRACE地址,然后就会产生文件了,这就是通过GET参数触发

   而通过POST请求也是这样,如果你的POST参数中带有XDEBUG_TRACE才会有回溯
```
           $.ajax({
               url : '/index.php',
               type : 'post',
               data : {
                   a : 1,
                   XDEBUG_TRACE : 11 //这个值随便设置,如果你不设置就不会POST这个字段上去!
               }
           });
```

4. 函数触发  
   在测试前请先确认配置xdebug.auto_trace=off(关闭自动回溯)

   然后找到你要开始追踪回溯的代码位置调用xdebug_start_trace函数,再在要停止追踪回溯的位置调用xdebug_stop_trace函数,这样就会生成回溯信息,并且是对你开始和结束trace函数之间的代码进行记录,其它无关的代码是不记录的

   样例代码:
```
            $str = 'abc';
            $str1 = substr($str, 0, 2);
            xx('a', 'b');

            function xx($a, $b){
                xdebug_start_trace(); //开始记录回溯
                $x = array();
                array_push($x, $a);
                print(222);
                array_push($x, $b);
                xdebug_stop_trace(); //结束记录回溯
                yy();
                return $x;
            }

            function yy(){
                print_r(123);
            }
```
   并且要注意,通过函数触发的话并不需要什么配置,你只要开启了扩展就可以,就是只保留zend_extension="xdebug.dll"就可以,其它xdebug的相关配置可以完全不配置,如果有文件名定制需求就再保留xdebug.trace_output_name选项就足够了

   然而如果你开启了xdebug.auto_trace其实相当于让PHP启动时就自动执行xdebug_start_trace函数,于是会报错说这个函数已经执行过了,所以为避免麻烦请不要开启xdebug_start_trace

   这个调试更加精准,因为如果整个运行周期都回溯下来,起码有成千上万行,查找成为了艰难的事情

   而函数触发时就缩小了你需要的范围,查找就快捷了很多!

5. php.ini文件中的配置如下：
```
    [xdebug]
    zend_extension ="e:/wamp64/bin/php/php5.6.35/zend_ext/php_xdebug-2.5.5-5.6-vc11-x86_64.dll";php加载xdebug
    xdebug.remote_enable=on
    xdebug.remote_autostart=off;开启远程调试自动启动
    xdebug.remote_handler=dbgp;用于sublime远程调试的应用层通信协议
    xdebug.remote_host=127.0.0.1;允许连接的sublime的IP地址
    xdebug.remote_port=9000;反向连接sublime使用的端口
    xdebug.profiler_enable = on
    xdebug.profiler_output_name = cachegrind.out.%t.%p
    xdebug.profiler_output_dir ="e:/wamp64/xdebug";xdebug 的数据文件目录
    xdebug.auto_trace = On ;开启自动跟踪回溯
    xdebug.trace_output_dir="e:/wamp64/xdebug";设置回溯信息输出目录
    xdebug.trace_output_name=trace.%R.%t;设置回溯信息输出文件名
    xdebug.collect_params=3;通过设置collect_params选项值为3开启参数记录
    xdebug.show_exception_trace = On ;开启异常跟踪
    xdebug.show_local_vars=0
    xdebug.collect_vars = On ;收集变量
    xdebug.collect_return = On ;收集返回值
    xdebug.collect_params = On ;收集参数
    #如果设得太小,函数中有递归调用自身次数太多时会报超过最大嵌套数错
    xdebug.max_nesting_level = 10000
```

## Sublime Text3 中的配置

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

3. 使用其他编辑器如 phpstorm、eclipse等xdebug配置也一样。

