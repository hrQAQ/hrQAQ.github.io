---
title: 「nginx」学习笔记
date: 2022-9-30 19:36:35
tags: 
---

大部分内容在课件里面都有写了，这里记录一些比较重要的点

## nginx介绍

Nginx是一款轻量级的Web服务器、反向代理服务器，由于它的内存占用少，启动极快，高并发能力强，在互联网项目中广泛应用。

## nginx原理

> nginx has one master process and several worker processes. The main purpose of the master process is to read and evaluate configuration, and maintain worker processes. Worker processes do actual processing of requests. nginx employs event-based model and OS-dependent mechanisms to efficiently distribute requests among worker processes. The number of worker processes is defined in the configuration file and may be fixed for a given configuration or automatically adjusted to the number of available CPU cores 

nginx在启动后系统运行一个master process，这个进程的作用是查看配置文件并维护worker process。nginx中真正起到转发请求作用的其实是这些worker process。master process通过阅读配置文件得知用户规定了哪些server，每一个server对应着一个worker process。所以学会使用nginx，其实就是学会写nginx的配置文件。

## 查找nginx相关进程

```shell
ps -aux | grep nginx
```

## 查看nginx的错误日志

> 出了问题不要慌，打开日志看一下

查看日志的时候需要从尾部开始查看

```shell
tail -f /var/log/nginx/error.log
```

## 配置静态网页

```nginx
# curl http://localhost:80 会访问这个
server {
    listen       80;	# 监听的端口
    server_name  localhost;		# 区分server的标识符

    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

# curl http://nginx-dev:80 会访问这个
server{
    listen 80;
    server_name nginx-dev;#主机名
    
    location / {
        root /home/AdminLTE-3.2.0;
        index index.html index2.html index3.html;
    }
  
}
```



> Reference：
>
> [nginx一小时精讲](https://www.bilibili.com/video/BV1rG4y1e7BQ?share_source=copy_web&vd_source=64521a646fa1bf3b43544380867c980b的笔记记录，https://www.yuque.com/wukong-zorrm/cql6cz/ofesua)
>
> [配套课件	](https://www.yuque.com/wukong-zorrm/cql6cz/ofesua)
>
> [官方Guide](https://nginx.org/en/docs/beginners_guide.html)