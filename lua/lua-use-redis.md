# Lua链接redis数据库

直奔主题

### 1、安装redis-lua模块
```
$ sudo apt-get install libmysqlclient-dev
$ sudo luarocks  install redis-lua
```

### 2、编译代码（demo如下）
```
#!/usr/local/lua53/bin/lua

package.cpath = package.cpath .. ";/usr/local/luarocks52/lib/lua/5.2/?.so"
package.path = "/usr/local/luarocks52/share/lua/5.2/?.lua";

local redis = require "redis"

local client = redis.connect('192.168.2.50', 6379)
local response = client:ping()

client:set('usr:nrk', 10)
client:set('usr:nobody', 5)
local value = client:get('usr:nrk')  
print(value)
```
目前redis-lua模块支持lua5.1 & 5.2。目前测试5.3版本出错

`注`:一定设置好`path`&`cpath`变量。否则报错包找不到
