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

## centos 6.9 安装
### 依赖
```
yum -y install gcc
yum -y install pcre-devel
yum -y install zlib zlib-devel
yum -y install openssl openssl-devel
```
### 下载包，解压
```
wget http://nginx.org/download/nginx-1.12.2.tar.gz
tar -xzvf nginx-1.12.2
mv nginx-1.12.2 /usr/local/dev/
```
### 安装
```
cd /usr/local/dev/nginx-1.12.2

./configure
# 编译 并 安装
make
make install

# 查看nginx安装到哪里
whereis nginx

# 默认安装到这里
/usr/local/nginx/sbin/nginx
```
### 开机自启
```
vim /etc/rc.d/rc.local

# nginx 启动
/usr/local/nginx/sbin/nginx

# 赋予执行权限
chmod 755 /etc/rc.d/rc.local
```
### 快速启动脚本
```
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemon
#
# chkconfig: - 85 15
# description: Nginx is an HTTP(S) server, HTTP(S) reverse \
#   proxy and IMAP/POP3 proxy server
# processname: nginx
# config: /etc/nginx/nginx.conf
# config: /etc/sysconfig/nginx
# pidfile: /var/run/nginx.pid
# Source function library.
. /etc/rc.d/init.d/functions
# Source networking configuration.
. /etc/sysconfig/network
# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0
    nginx="/usr/local/nginx/sbin/nginx"
    prog=$(basename $nginx)
    NGINX_CONF_FILE="/usr/local/nginx/conf/nginx.conf"
[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx
    lockfile=/var/lock/subsys/nginx

start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
[ $retval -eq 0 ] && touch $lockfile
    return $retval
}

stop() {
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT
    retval=$?
    echo
[ $retval -eq 0 ] && rm -f $lockfile
    return $retval
    killall -9 nginx
}

restart() {
    configtest || return $?
    stop
    sleep 1
    start
}

reload() {
    configtest || return $?
    echo -n $"Reloading $prog: "
    killproc $nginx -HUP
    RETVAL=$?
    echo
}

force_reload() {
    restart
}

configtest() {
    $nginx -t -c $NGINX_CONF_FILE
}

rh_status() {
    status $prog
}

rh_status_q() {
    rh_status >/dev/null 2>&1
}

case "$1" in
    start)
        rh_status_q && exit 0
        $1
    ;;
    stop)
        rh_status_q || exit 0
        $1
    ;;
    restart|configtest)
        $1
    ;;
    reload)
        rh_status_q || exit 7
        $1
    ;;
    force-reload)
        force_reload
    ;;
    status)
        rh_status
    ;;
    condrestart|try-restart)
        rh_status_q || exit 0
    ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2
esac
```
```
# 1、将上面代码写到 nginx 文件中
vim /usr/bin/nginx

# 2、添加执行权限
chmod 755 nginx

# 3、可以在任意目录下使用 nginx start|stop|status|restart|…
nginx start
nginx stop
nginx status
nginx restart
…
```