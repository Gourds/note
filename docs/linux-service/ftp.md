**概述：** FTP文件服务器，用于文件上传下载。有两种模式
> 参阅
> - [http://phorum.com.tw/ShowPost/5609.aspx](http://phorum.com.tw/ShowPost/5609.aspx)



### 主动模式
![ftp-z.png](https://cdn.nlark.com/yuque/0/2020/png/2623495/1602319872021-47815e9c-5c5d-4084-8a28-feca9d000d93.png#align=left&display=inline&height=409&margin=%5Bobject%20Object%5D&name=ftp-z.png&originHeight=409&originWidth=426&size=73764&status=done&style=none&width=426)
> **主动模式：** 服务器向客户端敲门，然后客户端开门
> PORT（主动）方式的连接过程是：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。当需要传送数据时，客户端在命令链路上用PORT命令告诉服务器：“我打开了XXXX端口，你过来连接我”。于是服务器从20端口向客户端的XXXX端口发送连接请求，建立一条数据链路来传送数据。

### 被动模式
![ftp-b.png](https://cdn.nlark.com/yuque/0/2020/png/2623495/1602319912347-d95e537a-e8cf-4a40-8af8-e50e2b32051a.png#align=left&display=inline&height=415&margin=%5Bobject%20Object%5D&name=ftp-b.png&originHeight=415&originWidth=526&size=88078&status=done&style=none&width=526)
> **被动模式：** 客户端向服务器敲门，然后服务器开门
> PASV（被动）方式的连接过程是：客户端向服务器的FTP端口（默认是21）发送连接请求，服务器接受连接，建立一条命令链路。当需要传送数据时，服务器在命令链路上用PASV命令告诉客户端：“我打开了XXXX端口，你过来连接我”。于是客户端向服务器的XXXX端口发送连接请求，建立一条数据链路来传送数据。

