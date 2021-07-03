### 常用配置
#### 带basic验证的反向代理
```bash
printf "taiyouxi:$(openssl passwd -crypt your_password)\n" >/usr/local/nginx/passwd.db
#nginx add
server {
        listen       18081;
        server_name  127.0.0.1;
        auth_basic "name";
        auth_basic_user_file /usr/local/nginx/passwd.db;
        access_log /var/log/nginx/test.log
        error_log /var/log/nginx/test-error.log
        location / {
                   proxy_pass http://127.0.0.1:8081;
                   proxy_set_header Host $host:$server_port;
                   proxy_set_header X-Real-IP $remote_addr;
                   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                   proxy_redirect off;
          }       #charset koi8-r;
}
```
#### 禁用trace方法
```bash
#关闭默认banner(添加在全局配置http段）
server_tokens off;
#关闭HTTP的TRACE方法（添加在配置文件server段或location段内，作用域不同）
if ($request_method = TRACE) {
  return 403;
}
```
#### 简单文件服务器
```bash
server {  
        listen       9000;        #端口  
        server_name  localhost;   #服务名  
        charset utf-8; # 避免中文乱码
        root    /usr/share/data;  #显示的根索引目录，注意这里要改成你自己的，目录要存在  
        location / {
            autoindex on;             #开启索引功能  
            autoindex_exact_size off; # 关闭计算文件确切大小（单位bytes），只显示大概大小（单位kb、mb、gb）  
            autoindex_localtime on;   # 显示本机时间而非 GMT 时间  
        }
    }
```
### 常见错误
问题：`413 Request Entity Too Large`
> 当请求报文大小大于1M时触发报错
> 解决方法：调大配置中的相关配置，添加`client_max_body_size 10m;`到`http`、`server`或`location`里面都可以，具体区别如下

| 位置 | 作用 |
| --- | --- |
| http | 控制着所有nginx收到的请求 |
| server | 控制该server收到的请求报文大小 |
| location | 只对匹配了location 路由规则的请求生效 |

```xml
#/etc/nginx/nginx.conf
http{
  ...
	client_max_body_size 10m;
  ...
}
```
​

​

​

​

​

​

​

