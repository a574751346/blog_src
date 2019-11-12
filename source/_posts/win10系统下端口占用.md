title: 【win10】端口已被占用
author: Meredith Ma
tags:
  - 操作系统
  - win10
  - 端口
categories:
  - 操作系统
date: 2018-07-20 18:50:00
update: 2018-07-20 00:00:00
---
这种情况只需要利用cmd命令行查出哪个进程异常占用了端口，终止此程序即可

### 1.查询是哪个进程（PID）占用了端口	

>netstat -ano | findstr port	

port替换为被占用的端口号

![upload successful](\\images\pasted-0.png\)	

假设端口号为4000，返回结果的最右侧一串数字（8684）即为PID	

### 2.使用PID查询是哪个程序占用了端口	

>tasklist | findstr 8684

### 3.终止占用端口的程序	

打开任务管理器，详细信息选项卡中可以看到PID对应的程序，终止此程序即可解决本端口被占用的问题