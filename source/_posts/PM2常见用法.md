---
title: PM2常见用法
date: 2018-03-25 12:25:46
tags:
    - nodejs
    - PM2
    - 守护进程
---

[Github](https://github.com/Unitech/pm2)

**definition:**

* PM2 is a General Purpose Process Manager and a Production Runtime for Node.js apps with a built-in Load Balancer.（PM2是一个带有负载均衡功能的Node应用进程管理工具）




**Key features:**

- Simple and efficient process management (start/stop/restart/delete/show/monit)

- Keep your application ALWAYS ONLINE with auto restarts and init system script generation

- Clusterize Node.js Applications without code change to increase performance and reliability

- Hot Reload Node.js Applications without extra configuration

  <!-- more -->


**常见的PM2使用命令**


* 安装pm2

  ``` 
  $ npm install pm2 -g
  ```


* 启动应用

    ```
    $ pm2 start app.js
    ```

* 查看应用列表

    ```
    $ pm2 list
    ```

* 快速管理进程

    ```
    $ pm2 stop     <app_name|id|'all'|json_conf>
    $ pm2 restart  <app_name|id|'all'|json_conf>
    $ pm2 delete   <app_name|id|'all'|json_conf>
    ```

* 查看当前进程列表以及各个进程使用情况

    ```
    $ pm2 monit
    ```

* 查看某个进程更多的细节

  ```
  $ pm2 describe <id|app_name>
  ```

* 日志的查看

    ```
    $ pm2 logs
    ```

* 热加载(热加载允许在不停机的情况下更新应用)

    ```
    $ pm2 reload all
    ```


**高级使用方式**

PM2还支持采用集群的方式去部署nodejs服务，而且还有些可视化的模块可以监测nodejs的服务，也使得运维人员变得更加的轻松。



**总结**

其实之前就使用一些其他的nodejs进程守护模块，比如forever，我也咨询了一些前端工程师他们之间的区别，整体来说区别不大，PM2出来的相对较晚，所以功能相对较为完善，也有一些好的可是话工具方式运维人员使用。



[关于forever和pm2之间比较的讨论](http://strong-pm.io/compare/)