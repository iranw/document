
下载
$ wget -c http://www.lua.org/ftp/lua-5.3.1.tar.gz
$ tar -zxvf lua-5.3.1.tar.gz
$ cd lua-5.3.1

依赖解决
$ sudo apt-get install libreadline-dev

编译
$ sudo make linux
$ sudo make test    #检查是否编译成功

安装
$ sudo make install INSTALL_TOP=/usr/local/lua53


luarocks 安装

$ ./configure --prefix=/usr/local/luarocks --lua-version=5.3 --with-lua=/usr/local/lua53
$ make build
$ sudo make install


问题1：tdin:1: module 'luasql.mysql' not found:
package.cpath = package.cpath .. ";/usr/lib/i386-linux-gnu/lua/5.1/?.so"
问题2： undefined symbol: luaL_optint
https://github.com/keplerproject/luasql/issues/30