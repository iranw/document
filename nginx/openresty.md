### ngx-openresty编译参数

```
$ ./configure --prefix=/usr/local/ngx_openresty --with-luajit --without-http_redis2_module --with-http_iconv_module --with-http_auth_request_module
$ make
$ sudo make install
```

###### 开启调试模式
```
$ ./configure --prefix=/usr/local/ngx_openresty --with-luajit --without-http_redis2_module --with-http_iconv_module --with-http_auth_request_module --with-debug
```