## 进入指定源码目录
cd /usr/src/

## 获取 nginx 源码
apt source nginx

## 安装相关依赖与一些相关软件
apt install vim wget git nginx dpkg-dev libpcre3-dev libssl-dev libxslt1-dev libgd2-xpm-dev libgeoip-dev -y

## 获取 nginx 编译参数，一会备用
nginx -V
>
--with-cc-opt='-g -O2 -fPIE -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -fPIE -pie -Wl,-z,relro -Wl,-z,now' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-pcre-jit --with-ipv6 --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_addition_module --with-http_dav_module --with-http_geoip_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module --with-http_v2_module --with-http_sub_module --with-http_xslt_module --with-stream --with-stream_ssl_module --with-mail --with-mail_ssl_module --with-threads

## lua 模块
git 地址：https://github.com/openresty/lua-nginx-module

1. 安装 luajit2
home: http://luajit.org
git: https://github.com/openresty/luajit2

```
git clone https://github.com/openresty/luajit2
cd luajit2 && make && make install && cd ..
```

2. 下载 NDK
git: https://github.com/simplresty/ngx_devel_kit
```
git clone https://github.com/simplresty/ngx_devel_kit
```

3. 下载 lua-nginx-module
git: https://github.com/openresty/lua-nginx-module/
```
git clone https://github.com/openresty/lua-nginx-module/
```
4. 修改编译参数
- 在 `--with-ld-opt` 中添加 `,-rpath,/usr/local/lib`
- 在编译参数中追加 `--add-module=/usr/src/ngx_devel_kit \
         --add-module=/usr/src/lua-nginx-module`

完整编译参数如下:
--with-cc-opt='-g -O2 -fPIE -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -fPIE -pie -Wl,-z,relro -Wl,-z,now,-rpath,/usr/local/lib' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-pcre-jit --with-ipv6 --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_addition_module --with-http_dav_module --with-http_geoip_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module --with-http_v2_module --with-http_sub_module --with-http_xslt_module --with-stream --with-stream_ssl_module --with-mail --with-mail_ssl_module --with-threads --add-module=/usr/src/ngx_devel_kit --add-module=/usr/src/lua-nginx-module

5. 重新编译 nginx
```
LUAJIT_LIB=/usr/local/lib LUAJIT_INC=/usr/local/include/luajit-2.1 ./configure --with-cc-opt='-g -O2 -fPIE -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -fPIE -pie -Wl,-z,relro -Wl,-z,now,-rpath,/usr/local/lib' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-pcre-jit --with-ipv6 --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_addition_module --with-http_dav_module --with-http_geoip_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module --with-http_v2_module --with-http_sub_module --with-http_xslt_module --with-stream --with-stream_ssl_module --with-mail --with-mail_ssl_module --with-threads --add-module=/usr/src/ngx_devel_kit --add-module=/usr/src/lua-nginx-module
make -j2 # 使用 2 个线程进行编译，此处建议为 cpu 的 2 倍，过多会卡
# make install # 不用执行。执行后会覆盖 /usr/share/nginx/sbin/nginx

# 手动执行
# test ! -f '/usr/sbin/nginx' \
#        || mv '/usr/sbin/nginx' \
#                '/usr/sbin/nginx.old'
# cp objs/nginx '/usr/sbin/nginx'

nginx -V 确认新编译的是否包含 Lua 模块
停止原来的 nginx。
启动新的 nginx。
不可使用重启。
```

6. 测试
```
# 在 /etc/nginx/sites-enable/default 的 server 中添加如下内容
server {
    ...
    location /lua {
        content_by_lua '
            ngx.header.content_type = "text/html";
            ngx.say("Hello Lua.");
        ';
    }
    ...
}
# 检测语法是否正确
nginx -t

# 重新加载 nginx
nginx -s reload
```
