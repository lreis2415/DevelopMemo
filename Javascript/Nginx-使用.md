---
title: Nginx 使用
toc: true
description: ' '
date: 2018-07-26 18:41:04
tags:
categories:
---

# Nginx 使用

官网：http://nginx.org/

文档： http://nginx.org/en/docs/windows.html

- windows：下载文件并解压到目标目录即可
- CentOS：`yum install nginx`

## 运行

- `start nginx`
	 `nginx -s stop` 	fast shutdown  
-  `nginx -s quit`    graceful shutdown
- `nginx -s reload`  修改配置、启动新的工作进程时

> tasklist /fi "imagename eq nginx.exe"

## 配置Nginx为Win系统服务

> [windows系统下将nginx作为系统服务启动](https://blog.csdn.net/yan_fang/article/details/52584359)
>
> https://github.com/kohsuke/winsw/releases

- 将下载的winsw的exe放到nginx安装目录下，并重命名为`nginx-service `
- 新建`nginx-service.xml`文件（须与可执行文件名相同），其内容如下（executable目录不能有空格）

 ```xml
<service>    
    <id>nginx</id>    
    <name>nginx</name>    
    <description>nginx</description>    
    <executable>D:/Programs/nginx/nginx.exe</executable>    
    <logpath>D:/Programs/nginx/logs</logpath>    
    <logmode>roll</logmode>    
    <depend></depend> 
    <stopexecutable>D:/Programs/nginx/nginx.exe -s stop</stopexecutable>
</service>
 ```

- 命令行中运行`nginx-service.exe install `
  - 其他命令 `nginx-service.exe uninstall/stop/start`
- 查看是否成功：`services.msc`
- 启动/停止服务：`net  start/stop nginx`( 若报错 5，则需要使用管理员模式执行命令行)

>  很多博客里面写的配置包含下面两行内容：
>    ```xml
>  <startargument>-p D:/Programs/nginx</startargument> 
>  <stopargument>-p D:/Programs/nginx -s stop</stopargument>    
>    ```
>
>  可能由于版本的原因，会导致 nginx 服务无法启动（报1067）和停止
>
>  参考：http://elsafly.gitee.io/windows/winInstallNginxAsService/

## 配置

> 参考配置  http://www.nginx.cn/76.html

```yaml
# 运行用户
# user nobody;
# 启动进程,通常设置成和cpu的数量相等
worker_processes  1;
 
# 全局错误日志及PID文件
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
 
# pid        logs/nginx.pid;
 
# 工作模式及连接数上限
events {
    # epoll是多路复用IO(I/O Multiplexing)中的一种方式,
    # 仅用于linux2.6以上内核,可以大大提高nginx的性能
    # use   epoll; 
 
    # 单个后台worker process进程的最大并发链接数    
    worker_connections  1024;
 
    # 并发总数是 worker_processes 和 worker_connections 的乘积
    # 即 max_clients = worker_processes * worker_connections
    # 在设置了反向代理的情况下，max_clients = worker_processes * worker_connections / 4 
    # 为什么上面反向代理要除以4，应该说是一个经验值
    # 根据以上条件，正常情况下的Nginx Server可以应付的最大连接数为：4 * 8000 = 32000
    # worker_connections 值的设置跟物理内存大小有关
    # 因为并发受IO约束，max_clients的值须小于系统可以打开的最大文件数
    # 而系统可以打开的最大文件数和内存大小成正比，一般1GB内存的机器上可以打开的文件数大约是10万左右
    # 我们来看看360M内存的VPS可以打开的文件句柄数是多少：
    # $ cat /proc/sys/fs/file-max
    # 输出 34336
    # 32000 < 34336，即并发连接总数小于系统可以打开的文件句柄总数，这样就在操作系统可以承受的范围之内
    # 所以，worker_connections 的值需根据 worker_processes 进程数目和系统可以打开的最大文件总数进行适当地进行设置
    # 使得并发总数小于操作系统可以打开的最大文件数目
    # 其实质也就是根据主机的物理CPU和内存进行配置
    # 当然，理论上的并发总数可能会和实际有所偏差，因为主机还有其他的工作进程需要消耗系统资源。
    # ulimit -SHn 65535
 
}
 
http {
    #设定mime类型,类型由mime.type文件定义
    include    mime.types;
    default_type  application/octet-stream;
    #设定日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
 
    access_log  logs/access.log  main;
 
    # sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，
    # 对于普通应用，必须设为 on,
    # 如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，
    # 以平衡磁盘与网络I/O处理速度，降低系统的uptime.
    sendfile     on;
    #tcp_nopush     on;
 
    #连接超时时间
    #keepalive_timeout  0;
    keepalive_timeout  65;
    tcp_nodelay     on;
 
    #开启gzip压缩
    gzip  on;
    gzip_static on;
    gzip_disable "MSIE [1-6].";
 
    #设定请求缓冲
    client_header_buffer_size    128k;
    large_client_header_buffers  4 128k;
 
    #设定虚拟主机配置
    server {
        #侦听80端口
        listen    80;
        #定义使用 www.nginx.cn访问
        server_name  www.nginx.cn;
 
        #定义服务器的默认网站根目录位置
        root html;
 
        #设定本虚拟主机的访问日志
        access_log  logs/nginx.access.log  main;
 
        #默认请求
        location / {       
            #定义首页索引文件的名称
            index index.php index.html index.htm;   
        }
        # 反向代理
        location /solim {
             proxy_pass http://localhost:8080/cybersolim;
             proxy_set_header  X-Real-IP  $remote_addr;
        }
		
        # 定义错误提示页面
        error_page   500 502 503 504 /50x.html;
        location = /50x.html {
        }
 
        # 静态文件目录，nginx自己处理
        location ~ ^/(images|javascript|js|css|flash|media|static)/ {         
            #过期30天，静态文件不怎么更新，过期可以设大一点，
            #如果频繁更新，则可以设置得小一点。
            expires 30d;
        }
        # 静态文件
        location ~* \.(html|htm|gif|jpg|jpeg|bmp|png|ico|txt|js|css)$ {         
             # 过期时间，d 表示 天， 如果频繁更新，可以设置得小一点。
             expires 30d;
             access_log off;
        }
        #PHP 脚本请求全部转发到 FastCGI处理. 使用FastCGI默认配置.
        location ~ .php$ {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
 
        #禁止访问 .htxxx 文件
        location ~ /.ht {
            deny all;
        }
    }
}
```

### Location 

#### 匹配规则

```yaml
=     # 进行普通字符精确匹配
^~    # ^~表示普通字符匹配，如果该选项匹配，只匹配该选项，不匹配别的选项，一般用来匹配目录
~     # 波浪线表示执行一个正则匹配，区分大小写
~*    # 表示执行一个正则匹配，不区分大小写
@     # "@" 定义一个命名的 location，使用在内部定向时，例如 error_page, try_files
```

#### 优先级：

1. =前缀的指令严格匹配这个查询。如果找到，停止搜索。
2. 所有剩下的常规字符串，最长的匹配。如果这个匹配使用^〜前缀，搜索停止。
3. 正则表达式，在配置文件中定义的顺序。
4. 如果第3条规则产生匹配的话，结果被使用。否则，使用第2条规则的结果。

#### root 和 location

配置中`root`处理结果：root 路径＋location 路径 

### JSON形式的错误信息

Nginx 默认返回html形式的错误信息。若需要返回JSON格式，则参考以下示例：

```jade
location /500 {
    default_type application/json;
    return 500 '{"code":500, "message": "Unknown Error"}';
}
```

https://gist.github.com/weapp/93606941a41273eb15bb

https://gist.github.com/simlegate/75b18359316cc33d8e20



### 虚拟主机配置

参考：[Linux 系列（六）——Nginx实现多虚拟主机配置](https://blog.csdn.net/daybreak1209/article/details/51546332)

---

参考资料

[Nginx配置详解](https://www.cnblogs.com/knowledgesea/p/5175711.html)

[Nginx 服务器安装及配置文件详解](https://www.cnblogs.com/bluestorm/p/4574688.html)

[centos 7 编译安装nginx 及添加 nginx 到系统服务](https://www.cnblogs.com/zyjfire/p/7605623.html)

[Nginx 启用gzip压缩](https://www.cnblogs.com/yingsong/p/6047311.html)



