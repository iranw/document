
### PHP代码执行过程运行示意图
![https://github.com/iranw/document/blob/master/static/php-run-list.png?raw=true](https://github.com/iranw/document/blob/master/static/php-run-list.png?raw=true)

`词法分析`就是对PHP代码分割成一个个的“单元”(TOKEN)
`语法分析`就是将这些单元(TOKEN)转化为Zend Engine可执行的操作

**php代码输入**->**词法分析**->**语法分析**->**ZendEngion顺序执行**->**输出**

<br><br>
### PHP SAPI执行流程
```
模块初始化阶段(MINIT)      #此次过程只执行一次(Apache或者命令行程序启动时出发)
        ↓
模块激活阶段(RINIT)        #请求阶段触发(例如url请求)，注册扩展模块/注册常量等
        ↓
停止模块(RSHUTDOWN)        #请求结束停用模块(请求执行完触发),对应RINIT模块
        ↓
关闭模块(MSHUTDOWN)        #SAPI生命周期结束(例如web服务器关闭等)触发,对应MINIT模块
```

<br><br>
### 单进程SAPI生命周期
```
$./bin/php -f test.php              #执行php
        ↓
Call each extension's MINIT         #模块初始化
        ↓
Request test.php                    #分析php
        ↓
Call each extension's RINIT         #模块激活
        ↓
Execute test.php                    #执行文件
        ↓
Call each extension's RSHUTDOWN     #停止模块
        ↓
Finish cleaning up after test.php   #清理信息
        ↓
Call each extension's MSHUTDOWN     #关闭模块
        ↓
Terminate php                       #终止php
```

<br><br>
### PHP文件服务器端执行流程
以`php ./test.php` 为例
```
启动php以及Zend引擎
加载注册的扩展模块
读取脚本文件(include)
Zend引擎对文件词法分析
Zend引擎对文件语法分析
将php文件编译成opcode码
执行
```
`注1`:如果有opcode码，则直接执行opcode码，不进行转化

`注2`:PHP的opcode就是Zend虚拟机中的指令

`注3`:虚拟机都是在某种较底层的语言上抽象出两外一种语言，并且实现其它辅助功能，如内存管理，垃圾回收机制等，以减少程序员在具体实现上的工作，从而可以将更多的时间和精力投入到业务逻辑中。``Java虚拟机``,`.NET虚拟机`，`Zend虚拟机`等等都是范畴之内

`注4`:wiki中虚拟机的定义是： 虚拟机（Virtual Machine），在计算机科学中的体系结构里，是指一种特殊的软件， 他可以在计算机平台和终端用户之间创建一种环境，而终端用户则是基于这个软件所创建的环境来操作软件。 在计算机科学中，虚拟机是指可以像真实机器一样运行程序的计算机的软件实现。
虚拟机是一种抽象的计算机，它有自己的指令集，有自己的内存管理体系。 在此类虚拟机上实现的语言比较低抽象层次的语言更加明了，更加简单易学。

`注5`:语法分析就是将我们要执行的php源文件，去掉空格，去掉注释，切分为一个个的标记(Token),并且处理程序的层级结构.

`注6`:语法分析就是接受的标志(Token)序列,根据定义的语法规则，来执行一些动作.

`注`:php程序->C语言(Zend虚拟机)->汇编语言->机器码

`注`:从一种语言转化为另外一种语言称之为编译。这两种语言分别称为源语言&目标语言。通常目标语言比源语言更低级(或者说更底层).语言转化的编译过程是由编译器来完成。

`注`:编译器过程:词法分析->语法分析->语义分析->中间代码生成->代码优化->目标代码生成等

`注`:被称为编译型语言，一般是由于在程序执行之前有一个翻译的过程，关键点就是一个形式上完全不同的等价程序生成。(php为解释型语言)