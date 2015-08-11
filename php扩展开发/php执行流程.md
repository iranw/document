
### PHP代码执行过程运行示意图
![https://github.com/iranw/document/blob/master/static/php-run-list.png?raw=true](https://github.com/iranw/document/blob/master/static/php-run-list.png?raw=true)

`词法分析`就是对PHP代码分割成一个个的“单元”(TOKEN)
`语法分析`就是将这些单元(TOKEN)转化为Zend Engine可执行的操作

**php代码输入**->**词法分析**->**语法分析**->**ZendEngion顺序执行**->**输出**

<br><br>
### PHP SAPI执行流程
```
模块初始化阶段(MINIT)      #次过程只执行一次(Apache或者命令行程序启动时出发)
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