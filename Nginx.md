## Nginx

### 常用配置
- 配置文件： /etc/nginx/nginx.conf 副配置 /etc/nginx/conf.d/*.conf
- 开启 $ systemctl start nginx
- 重启 $ nginx -s reload
- 关闭 $ nginx -s stop
- 简单配置
```
server {
  server_name 118.24.23.200;
  root /data/test-nginx;
  index index.html;
  location / {
  # 这里不配置root就返回默认的
  }
  # 设置静态图片
  location ^~ /static/ {
    root /data/test-nginx
  }
  # 设置上传图片地址转发
  location ^~ .*\.(gif|jpg|jpeg|png)$ {
    proxy_pass 转发的地址
  }
}
```

## 架构简介
主线程master（管理worker进程，接收外界信号）
workder进程（实际执行的进程）

## 设置跨域
```
add_header Access-Control-Allow-Origin *;
add_header Access-Control-Allow-Headers X-Requested-With;
add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
```