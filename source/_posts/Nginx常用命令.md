---
title: Nginx常用命令
author: Tomatoro
comments: true
tags: [Nginx]
top: 0
date: 2019-10-23 11:28:24
---


启动nginx

```bash
nginx
```

关闭nginx

```bash
nginx -s stop
```

退出nginx

```bash
nginx -s quit
```

重启nginx

```bash
nginx -s reload
```

<!-- more -->

使用brew下载nginx

```bash
brew install nginx
```

查看目前执行的任务

```bash
brew services list
```

进入nginx 文件

```bash
cd /usr/local/etc/nginx
```

查看nginx的配置

```bash
vi nginx.conf
```

查看nginx配置有没有生效

```bash
nginx -t

nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok

nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful
```

启动nginx服务

```bash
brew services start nginx
```

重启nginx服务

```bash
brew services restart nginx
```

停止nginx服务

```bash
brew services stop nginx
```

查看是否启动

```bash
ps -ef|grep nginx

0 63206 1 0 5:21下午 ?? 0:00.00 nginx: master process nginx

-2 63274 63206 0 5:23下午 ?? 0:00.01 nginx: worker process

501 65848 62966 0 5:52下午 ttys001 0:00.00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn nginx
```

停止当前master的服务

```bash
sudo kill -QUIT 63206
```