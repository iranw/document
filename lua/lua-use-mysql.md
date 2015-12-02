# Lua链接mysql数据库

直奔主题

### 1、安装luasql-mysql模块
```
$ sudo apt-get install libmysqlclient-dev
$ sudo luarocks --from=http://rocks.luarocks.org/dev install luasql-mysql cvs-1 MYSQL_INCDIR=/usr/include/mysql
```
不用使用`sudo luarocks install luasql-mysql MYSQL_INCDIR=/usr/include/mysql`来安装模块，用此方法安装的模块暂不支持5.3。请使用上面方法安装

### 2、查看luasql-mysql模块信息
```
$ luarocks list
$ luarocks show luasql-mysql

LuaSQL-MySQL cvs-1 - Database connectivity for Lua (MySQL driver)

LuaSQL is a simple interface from Lua to a DBMS. It enables a Lua program to
connect to databases, execute arbitrary SQL statements and retrieve results in
a row-by-row cursor fashion.

License:    MIT/X11
Homepage:   http://www.keplerproject.org/luasql/
Installed in:   /usr/local/luarocks

Modules:
    luasql.mysql (/usr/local/luarocks/lib/lua/5.3/luasql/mysql.so)

iranw@ubuntu:~$
```
记住luasql.mysql的安装目录

### 3、编写代码
demo如下
```
#!/usr/local/lua53/bin/lua
-- file: 'mysql.lua'
-- -- desc: use mysql lib

package.cpath = package.cpath .. ";/usr/local/luarocks/lib/lua/5.3/?.so"

print(package.cpath)
print("******\nhello,world\n")


luasql = require "luasql.mysql"     
-- 在5.1版本可以直接使用 `require "luasql.mysql"` 
-- 但是5.2&5.3版本必须使用`luasql = require "luasql.mysql"` 此处是个坑，千万留意 

env = assert (luasql.mysql())

conn = env:connect("test","admin","123456","127.0.0.1",3306)

cur = conn:execute("SELECT * FROM t_demo")

row = cur:fetch({},"a")
while row do
    print(row.name)
    row = cur:fetch(row,"a")
end

conn:close()
env:close()
```

执行即可