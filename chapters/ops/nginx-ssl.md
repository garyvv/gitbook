# nginx https正向代理

- 编译安装nginx 和 connect 模块

download ngx_http_proxy_connect_module

link: https://github.com/chobits/ngx_http_proxy_connect_module

```
wget http://nginx.org/download/nginx-1.9.12.tar.gz
tar -xzvf nginx-1.9.12.tar.gz
cd nginx-1.9.12/
patch -p1 < /data/download/ngx_http_proxy_connect_module-master/patch/proxy_connect.patch
./configure --add-module=/data/download/ngx_http_proxy_connect_module-master --with-http_stub_status_module --with-http_ssl_module
make && make install

/usr/local/nginx/sbin/nginx -V
```

> nginx.conf  example

```
# user  www www;

worker_processes auto;

error_log  /usr/local/nginx/logs/nginx_error.log  crit;

pid        /usr/local/nginx/logs/nginx.pid;

worker_rlimit_nofile 51200;

events
{
    use epoll;
    worker_connections 51200;
    multi_accept on;
}

http
{
    include       mime.types;
    default_type  application/octet-stream;

    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_max_body_size 50m;

    sendfile   on;
    tcp_nopush on;

    keepalive_timeout 60;

    tcp_nodelay on;

    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 256k;

    gzip on;
    gzip_min_length  1k;
    gzip_buffers     4 16k;
    gzip_http_version 1.1;
    gzip_comp_level 2;
    gzip_types     text/plain application/javascript application/x-javascript text/javascript text/css application/xml application/xml+rss;
    gzip_vary on;
    gzip_proxied   expired no-cache no-store private auth;
    gzip_disable   "MSIE [1-6]\.";

    log_format  access  '$remote_addr - $remote_user [$time_local] requesthost:"$http_host"; "$request" requesttime:"$request_time"; '
        '$status $body_bytes_sent "$http_referer" - $request_body'
        '"$http_user_agent" "$http_x_forwarded_for"';

    log_format  main  '{"timestamp": "$time_local", "remote_addr": "$remote_addr", "host": "$http_host", "server_addr": "$server_addr", "request_time": "$request_time", "remote_user": "$remote_user", '
        '"request": "$request", "status": "$status", "body_sent": "$body_bytes_sent", "http_referer":"$http_referer", "http_user_agent": "$http_user_agent", '
        '"http_x_forwarded_for": "$http_x_forwarded_for", "request_body": "$request_body"}';

    server_tokens off;
    access_log off;

    server
    {
    	resolver 8.8.8.8;

    	resolver_timeout 5s;

    	listen 0.0.0.0:80;

     	location / {
            proxy_pass $scheme://$host$request_uri;

            proxy_set_header Host $http_host;

            proxy_buffers 256 4k;
            proxy_max_temp_file_size 0;
            proxy_connect_timeout 30;
            proxy_cache_valid 200 302 10m;
            proxy_cache_valid 301 1h;
            proxy_cache_valid any 1m;
     	}

        access_log  /usr/local/nginx/logs/access.log main;
    }

     server {
         listen       443 ssl;

         server_name  localhost;

         ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
         ssl_ciphers         AES128-SHA:AES256-SHA:RC4-SHA:DES-CBC3-SHA:RC4-MD5;
         ssl_certificate     /squid/core/ca/cam.crt;
         ssl_certificate_key /squid/core/ca/cam.key;
         ssl_session_cache   shared:SSL:10m;
         ssl_session_timeout 10m;

         resolver 8.8.8.8;

         proxy_connect;
         proxy_connect_allow            443 563;
         proxy_connect_connect_timeout  10s;
         proxy_connect_read_timeout     10s;
         proxy_connect_send_timeout     10s;

         location / {
             proxy_set_header Host $host;
             proxy_pass $scheme://$host$request_uri;
         }

	 access_log  /usr/local/nginx/logs/ssl_access.log main;
     }

    include vhost/*.conf;
}
```