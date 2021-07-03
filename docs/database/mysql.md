### 常用命令
#### v5.0版本-账户管理
```bash
#1. 初始化admin密码
mysqladmin -u root password ab12
#2. 更改admin密码
mysqladmin -u root -p ab12 password djg345
#2. 新建用户并授权
# -a).增加一个用户test1密码为abc，让他可以在任何主机上登录，并对所有数据库有查询、插入、修改、删除的权限。
grant select,insert,update,delete on *.* to test1@"%" Identified by "abc";
# -b).增加一个用户test2密码为abc,让他只可以在localhost上登录，并可以对数据库mydb进行查询、插入、修改、删除的操作（localhost指本地主机，即MYSQL数据库所在的那台主机）
grant select,insert,update,delete on mydb.* to test2@localhost identified by "abc";
grant select on test.* to read_yy@"%" Identified by "123";
grant select on jpdata.* to read_yy@"%" Identified by "123";
grant ALL PRIVILEGES  on yy_userinfo.* to zz_userinfo@"%" Identified by "123";
# -c).如果你不想test2有密码，可以再打一个命令将密码消掉
grant select,insert,update,delete on mydb.* to test2@localhost identified by "123";
#3. 更改用户权限
update mysql.user set password=password('新密码') where User="testuser" and Host="localhost";
#4. 更改用户密码
update mysql.user set password=password('新密码') where User="testuser" and Host="localhost";
#5. 删除用户
Delete FROM user Where User='test' and Host='localhost';
#6. 禁用admin用户
 
#7. 查询所有用户及权限
SELECT DISTINCT CONCAT('User: ''',user,'''@''',host,''';') AS query FROM mysql.user;
#8. 刷新权限
FLUSH PRIVILEGES;
```
##### 创建数据库
```bash
CREATE DATABASE IF NOT EXISTS jumpserver DEFAULT CHARACTER SET utf8;
```
##### 密码重置
```bash
/etc/init.d/mysqld stop
safe_mysqld --skip-grant-tables --user=root &
update mysql.user set password=PASSWORD('新密码') where User='root'; 
flush privileges;
```
### 安装配置
#### docker快速启动
```bash
docker run --name mysql-gmtools \
-p 13306:3306 \
-v /opt/db/mysql:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:5.7.14 \
--character-set-server=utf8mb4 \
--innodb_buffer_pool_size=1600M \
--max-allowed-packet=33554432 \
--sql_mode=STRICT_ALL_TABLES \
 
#import
docker exec -i mysql-gmtools sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD"' < ./your.sql
```

---

### 一些问题
#### 为何系统上有两个mysql进程
![image.png](https://cdn.nlark.com/yuque/0/2020/png/2623495/1605754553478-60ce4d89-bd7a-47f6-ab9e-8cc0ee0300eb.png#align=left&display=inline&height=75&margin=%5Bobject%20Object%5D&name=image.png&originHeight=75&originWidth=1514&size=23524&status=done&style=none&width=1514)
> mysqld 与 mysqld_safe 的区别
> 直接运行mysqld程序来启动MySQL服务的方法很少见，mysqld_safe脚本会在启动MySQL服务器后继续监控其运行情况，并在其死机时重新启动它。用mysqld_safe脚本来启动MySQL服务器的做法在BSD风格的unix系统上很常见，非BSD风格的UNIX系统中的 mysql.server脚本其实也是调用mysqld_safe脚本去启动MySQL服务器的。它通常做如下事情：

> 1. 检查系统和选项。

> 2. 检查MyISAM表。

> 3. 保持MySQL服务器窗口。

> 4. 启动并监视mysqld，如果因错误终止则重启。

> 5. 将mysqld的错误消息发送到数据目录中的host_name.err 文件。

> 6. 将mysqld_safe的屏幕输出发送到数据目录中的host_name.safe文件。

