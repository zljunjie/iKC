# Networking


## 1. get和post的区别
```
1. 请求参数 get: 参数跟在host/path后面 | post: 表单数据存储在请求体内
2. 请求缓存 get 支持 post 不支持
3. 请求长度 get: 一般不超过2kb Post: 适合大规模数据
4. 安全性 get: 内容直接线上在url中 post: 数据放置到请求提中

Post支持上传的类型 
1: multipart/form-data
2: application/x-www-from-urlencodeed
3: raw
4: binary
```

## 2. HTTP协议
```
1. 超文本传输协议,在TCP之上,工作在应用层 (TCP 工作在传输层)
2. HTTP请求报文 (1 请求行 2 请求头 3空行 4请求体四部分)
    请求行
        请求方式
        URL
        版本 
    请求头
        Host 主机
        Accept 接受数据的类型
        Accept-Language 语言类型
        Accept-encoding 编码方式
        Contect-Type: 发送数据的类型
        Contect-Length 请求体内容长度
        Connection keep-alive
        Cookies 
        Pragma: no-cache
        User-Agent
        Referer 请求从哪个URL访问过来的, 可记录流量
        range
        If-Modify-since
        If-None-Match
    空行
    请求体


3. HTTP相应报文 (1 状态行 2 响应头 3空行 4响应体)
    状态行
        协议版本号
        状态码及状态
    响应头
        Server： 服务端信息
        Content-Type: 内容类型
        Content-Length 长度
        Content-Language 
        Last-Modified
        Cache-Control
        Date: 日期

```


## HTTP 1.0 1.1 2.0的区别
```
1.1版本
1. 支持长连接 alive-keep
2. 支持缓存
3. 带宽优化及连接的使用 -》 请求头中增加了range字段,支持只请求资源的一部分,返回码为206


2.0版本
1. 支持多路复用 允许使用单个http/2连接 发起多重请求
2. 二进制分帧 在应用层与传输层中间增加了一个二进制分帧层, 将信息分割成更小的消息和帧类型.
3. 首部压缩
其中将请求头封装成header Frame ,将请求体封装成Data Frame。
优势 ：
单链接多资源方式 减少了服务端的多连接压力,内存占用更少，连接的吞吐量更大
降低延时性


1.1之前 影响http性能的主要问题是 高延迟 其次是带宽
2.0支持多路复用后, 大幅度降低延迟性,带宽的大小成为了主要的影响因素


```

## TCP慢启动
```
TCP连接会随着时间进行自我协调,连接刚被创建时,会限制连接 最大速度,如果数据成功传输，会随着时间的延续提高传输的速度
http/2 支持多路复用,可以在一个连接中传输同个请求
```

## 3.TCP与UDP
```
TCP Transmission Control Protocol 传输控制协议
UPD User Data Protocol 用户数据报协议

TCP 可靠字节流服务，在信息交换之前,双方建立一个TCP链接(通过三次握手创建,四次挥手),提供超时重发,重复数据去重,数据检验,流量控制
UDP 不可靠数据传输服务,将数据打包传递给IP层将数据发送出去，不建立连接，传输速度快

UDP报头 8个字节
2字节16位源端口号 | 16位目的端口号
16位UDP长度	  | 16位UDP校验和
剩下的就是数据部分
报头支持最大64K, 如果超过64K需要在应用层进行分包分序

基于UDP实现的其他协议
1. DNS 域名解析协议
2. NFS Network Files System 网络文件系统
3. TFTP 简单文件传输协议
4. DHCP 动态主机配置协议
5. BOOTP 启动协议(用于无盘设备启动)

TCP报文头 20个字节
2字节 源端口 | 2字节 目的端口
4字节序列号
4字节确认号
...
数据部分

```

##### 三次握手
```
1 服务A向主机B发送一个含有同步序列号标志位的数据段(SYN),向主机B请求建立连接。(通过这个数据段主要表达两件事情,1 建立通讯, 2主机B可以通过哪个序列号作为起始数据段来返回)
2 主机B收到主机A后,用一个带有确认应答(ACK)和同步序列号(SYN)标志位的数据段响应主机A
3 主机A收到请求后,在发送一个确认应答(ACK) (要开始传输数据了)
```
##### 四次握手
```
1 主机A将控制位FIN置1,发起请求提出停止TCP连接
2 主机B收到FIN后做出响应,确认将此TCP连接关闭,并将ACK置1
3 主机B在反向提出关闭请求,将FIN置1
4 主机A接收到B的请求, 将ACK置1,关闭连接
```


### 七层模型
```
从上往下分别是
应用层 为应用程序提供服务
表示层 数据格式转化,数据加密
会话层 建立,管理，维护会话 				HTTP HTTPS FTP POP3 SMTP
传输层 建立,管理，维护端到端的连接   	TCP UDP 
网络层 IP选择及路由选择 				IP
数据链路层 提供介质访问和链路管理
物理层 物理层
```
