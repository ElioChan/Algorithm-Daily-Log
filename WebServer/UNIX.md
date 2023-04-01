该文档用于记录UNIX一书中的笔记 以及 运行实验遇到的问题

# Chap1 简介和TCP/IP

## 客户端和服务器端的基础代码
> 客户端
- 建立socket 得到文件描述符 返回值异常处理
- 设置服务器地址 
  - 对于IPV4和IPV6都有专门的比较好用的地址格式 但是最后都要强制类型转化为 sockaddr* 
  - 需要通过inet_pton()转化为网络地址 
- connect 通过socket fd连接到服务器地址
- read 通过socket fd开启读事务 阻塞 需要为读准备缓冲区
- 断开连接 根据情况不同判断断开连接的条件

> 服务器端
- 设置监听socket fd 
- 将服务器地址与socket fd绑定(bidn)
- 进行监听(listen函数)
- 获得连接 接受连接 accept

## 简单的时间获取客户端程序
这一段代码主要是通过访问13号端口 来获取服务器上的时间信息 

> Q1: 运行daytimetcpcli.out文件 显示connection refuesd错误
- 原因: 这个是连到本机的时间服务 然后本机的时间服务linux是默认不开启的 所以无法连接到服务器<br> 还有一点就是13号端口需要root权限才能开启(<1024的端口都需要权限) 所以需要加sudo
- 解决办法：先开启时间服务

> Q2: 开启时间服务 运行命令 make daytimetcpsrv 显示错误bind permission denied
- 原因：前面说过需要root权限 所以加sudo即可
