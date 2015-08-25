# (入门)开发php第一个扩展(hello_world)

本文讲述如何在php5.5.28环境开发一个简单的php extension(phpno_com扩展),总结一下制作流程

需求：制作一个`phpno_com`扩展，该扩展包含一个`hello_world`函数(该函数打印"hello,world"字符串)

状态：目前php环境已经编译ok，开发动态模块扩展

###步骤如下

### 1、使用`ext_skel`生成扩展框架
```
$ cd ~/php-5.5.28/ext   //进入源码ext目录
$ ./ext_skel --extname=phpno_com
$ cd phpno_com
```
`注`:目前ext扩展目录已经生成phpno_com扩展框架模块。目录结构如下


### 2、编辑config.m4文件

修改文件10~12行 删除dnl 结果如下


### 3、编辑php_iranw.h文件
编辑php_iranw.h文件，在`PHP_FUNCTION(confirm_phpno_com_compiled);`下行添加需要添加函数(hello_world)的格式段
```
PHP_FUNCTION(hello_world);
```

### 4、编辑iranw.c文件
在文件末尾添加函数具体实现 代码如下
```
PHP_FUNCTION(hello_world)
{
    printf("hello,world");
}
```
并在`zend_function_entry iranw_functions`代码块里面注册函数，将`PHP_FE(hello_world, NULL)`添加到`PHP_FE_END`上一行。具体如下


### 5、进入扩展目录`phpno_com`编译扩展
```
$ /usr/local/php/bin/phpize
$ ./configure --with-php-config=/usr/local/php/bin/php-config
$ sudo make
$ sudo make install
```

### 6、修改php.ini文件，加入扩展
```
[phpno_com]
extension=phpno_com.so
```

### 7、测试
```
<?php
hello_world();
```


