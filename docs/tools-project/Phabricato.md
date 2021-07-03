### 安装及启动
#### 使用Docker启动
```bash
version: '2'
services:
  phabricator:
    restart: always
    ports:
     - "62443:443"
     - "80:80"
     - "62022:22"
    volumes:
     - /srv/docker/phabricator/repos:/srv/repo
     - /srv/docker/phabricator/static:/srv/static
     - /srv/docker/phabricator/conf:/srv/phabricator/phabricator/conf
     - /srv/docker/phabricator/extensions:/srv/phabricator/phabricator/src/extensions
     - /srv/docker/phabricator/pha-keys:/hostkeys
    depends_on:
     - mysql
    links:
     - mysql
    environment:
     - MYSQL_HOST=mysql
     - MYSQL_USER=root
     - MYSQL_PASS=phabricator
     - PHABRICATOR_REPOSITORY_PATH=/srv/repo
     - PHABRICATOR_HOST=wiki2.taiyouxi.net
     - PHABRICATOR_HOST_KEYS_PATH=/hostkeys/persisted
     - ENABLE_APCU=true
    image: redpointgames/phabricator
  mysql:
    restart: always
    volumes:
     - /srv/docker/phabricator-db/mysql:/var/lib/mysql
    image: mysql:5.7.14
    environment:
     - MYSQL_ROOT_PASSWORD=phabricator
    command: [
      "--character-set-server=utf8mb4",
      "--max-allowed-packet=33554432",
      "--sql_mode=STRICT_ALL_TABLES",
      "--innodb_buffer_pool_size=1600M"
      ]
```
#### 注意事项
##### 不是迁移的情况可以使用(上面的是把配置全copy到容器外目录的时候使用的)
```bash
/srv/docker/phabricator/conf/local/local.json:/srv/phabricator/phabricator/conf/local/local.json
```
##### 本地配置如下(/srv/docker/phabricator/conf/local/local.json) 
```json
{
"notification.servers": [
  {
    "type": "client",
    "host": "wiki2.test.net",
    "port": 80,
    "protocol": "http",
    "path": "/ws/"
  },
  {
    "type": "admin",
    "host": "127.0.0.1",
    "port": 22281,
    "protocol": "http"
  }
],
"storage.mysql-engine.max-size": 67108864,
"repository.default-local-path": "/srv/repo",
"storage.local-disk.path": "/srv/static",
"pygments.enabled": true,
"diffusion.ssh-user": "git",
"phd.user": "git",
"phabricator.base-uri": "http://wiki2.test.net/",
"storage.default-namespace": "phabricator",
"mysql.pass": "phabricator",
"mysql.user": "root",
"mysql.host": "mysql"
}
```
#### 数据库补充说明
##### 如果需要启动时进行MysqlDB的初始化，可以在镜像这里进行设置

1. 修改挂载
```
  ...
  volumes:
    - mysql-master-data:/var/lib/mysql
    - ./init-db-sql/sakila-schema.sql:/docker-entrypoint-initdb.d/1-schema.sql
    - ./init-db-sql/sakila-data.sql:/docker-entrypoint-initdb.d/2-data.sql
    - ./init-db-sql/init-master.sh:/docker-entrypoint-initdb.d/3-init-master.sh
  ...  
```

2. 参考
> 参考：[https://hub.docker.com/_/mysql/](https://hub.docker.com/_/mysql/)
> 基础镜像会在初始化(仅首次运行)时按文件名顺序执行 /docker-entrypoint-initdb.d 下的 .sql .sh 等文件

#### 问题解决
##### 普通列表项目无法读取写入仓库问题
```bash
 #参考网址 https://github.com/RedpointGames/phabricator/issues/8
 chown 2000.2000 -R /srv/repo
 chmod 777 -R /srv/static
```
##### 配置问题
```bash
#略
```
迁移
```bash
 #迁移代码数据
 scp -r /mnt/raid/phabricator/repo/* 10.0.1.1:/srv/docker/phabricator/repos/
 chown -R 2000.2000 /srv/docker/phabricator/repos/
 #迁移附件数据
 scp -r /mnt/raid/phabricator/static/* 10.0.1.1:/srv/docker/phabricator/static/
 chmod -R 777 /srv/docker/phabricator/static/
 #迁移数据库
 scp /usr/local/src/dockers/phabricator/pha_src/dbbackup/20190625-030001.sql.gz 10.0.1.221:/data/pha-restore/
 docker cp /data/pha-restore/20190625-030001.sql.gz phabricator_mysql_1:/
 docker exec -it phabricator_mysql_1 /bin/bash
 gunzip -c 20190625-030001.sql.gz |mysql -uroot -pphabricator
```
