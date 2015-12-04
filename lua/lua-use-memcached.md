# Lua链接Memcached数据库

lua链接memcached首先需要安装几个必要的库

### 1、安装`cjson`库
```
$ luarocks install lua-cjson
```

### 2、安装`MessagePack`库
```
$ luarocks install lua-messagepack
```

### 3、安装memcached库
首先安装libmemcached的扩展包，安装方法参考http://www.phpno.com/php-memcached.html
其次利用luarocks安装lua-libmemcached扩展
```
$ luarocks install --server=http://luarocks.org/dev lua-libmemcached LIBMEMCACHED_DIR=/usr/local/libmemcached 
```

### 4、测试
安装完必须的库后，既可以参考下面代码链接memcached服务器
```
#!/usr/local/lua53/bin/lua

--[[
package.cpath = package.cpath .. ";/usr/local/luarocks52/lib/lua/5.2/?.so"
package.path = "/usr/local/luarocks52/share/lua/5.2/?.lua";
--]]

package.cpath = package.cpath .. ";/usr/local/luarocks/lib/lua/5.3/?.so"
package.path = "/usr/local/luarocks/share/lua/5.3/?.lua"

local libmemcached = require 'libmemcached'
local encoder = require 'cjson'
local mp = require 'MessagePack'

local encoder = {encode = mp.pack, decode = mp.unpack}

local c = libmemcached.new('--server=192.168.2.50:11211',encoder)

c:set("ccc","cccccalue")
```

注释：memcached库在lua5.2 5.3测试均通过

资料
- [https://github.com/akornatskyy/lua-libmemcached](https://github.com/akornatskyy/lua-libmemcached)