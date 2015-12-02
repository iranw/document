# Lua5.3环境配置

`Lua`作为`Nginx`以及`Redis`支持的脚本语言。越来越被重视。是`Lua`作为`Nginx`或者`Redis`的扩展开发，高效迅速。下面将开始学习lua的第一步-安装Lua环境


### 1、依赖问题解决
```
#首先安装依赖包libreadline
$ sudo apt-get install libreadline-dev
```

### 2、下载
```
$ wget -c http://www.lua.org/ftp/lua-5.3.1.tar.gz
$ tar -zxvf lua-5.3.1.tar.gz
$ cd lua-5.3.1
```

### 3、编译安装
```
$ sudo make linux                                   #根据不同系统选择不同命令
$ sudo make test                                    #检查是否编译成功
$ sudo make install INSTALL_TOP=/usr/local/lua53    #设定安装的目录
```

### 3、加入环境变量
修改/etc/profile文件，添加环境变量PATH
```
$ cat /etc/profile
if [ -d /etc/profile.d ]; then
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi

# 将下面两行加入到文件末尾
PATH=/usr/local/lua53/bin:$PATH
export PATH
$ source /etc/profile   #执行生效
$ echo $PATH            #检查是否生效
$ lua
Lua 5.3.1  Copyright (C) 1994-2015 Lua.org, PUC-Rio
> 
```

到此Lua环境配置到此结束
可以执行下面脚本来打印大`hello，world`
文件内容如下
```
#!/usr/local/lua53/bin/lua
-- file: 'hello-world.lua'
-- desc: demo print "hello,world" 

print("Hello,world")
```
执行
```
$ chmod +x hello-world.lua
$ ./hello-world.lua 
Hello,world
$
```

------
华丽丽的分割线
------

Lua 是一个短小精悍的脚本语言，很多功能官方都不内置支持，需要添加第三方模块库来支持。但是，这些第三方模块是由不同开发者制作，分散的放在不同地方，如果想要安装一个模块还要到处去找，况且下载完模块后，有时还需要手动编译，设置环境变量，把模块放到指定的 Lua 模块加载目录。反正，安装一个第三方 Lua 模块需要执行上述一连串操作，也是一个繁琐的过程。

对于这些繁琐，LuaRocks 可以简化以上安装 Lua 模块的操作。 LuaRocks 是 Lua 模块的安装和部署工具，类似于 Ruby 的 gem，Python 的 egg 和 Perl 的 cpan，它可以很方便的安装第三方 Lua 模块，而且你不需要关心模块之间的依赖关系，一条命令就可以很轻松地把模块安装部署好，省心，省力，不用去折腾安装 Lua 模块的细节。---引自网络

# Lua的库管理工具`Luarocks`安装

### 下载安装步骤
```
$ wget -c http://luarocks.org/releases/luarocks-2.2.2.tar.gz
$ tar zxvf luarocks-2.2.2.tar.gz
$ cd luarocks-2.2.2/
$ ./configure --prefix=/usr/local/luarocks --lua-version=5.3 --with-lua=/usr/local/lua53    #注意引用的lua版本以及目录
$ make build
$ sudo make install
```
将luarocks 加入环境变量，方法同上

### 常使用方法
```
$ luarocks install xxx
$ luarocks list
$ luarocks show xxx
$ luarocks remove xxx
$ luarocks help         #查看更多帮助
```

## 常见问题
------
问题1：tdin:1: module 'luasql.mysql' not found:
解决方案：package.cpath = package.cpath .. ";/usr/lib/i386-linux-gnu/lua/5.1/?.so"

问题2： undefined symbol: luaL_optint
解决方案：https://github.com/keplerproject/luasql/issues/30

问题3：Error: Could not find expected file mysql.h, or mysql.h for MYSQL
解决方案： $ sudo apt-get install libmysqlclient-dev
iranw@ubuntu:/usr/local/luarocks$ sudo ./bin/luarocks install luasql-mysql MYSQL_INCDIR=/usr/include/mysql