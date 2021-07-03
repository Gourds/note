### docker镜像源
```bash
cat /etc/docker/daemon.json
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com"
  ]
}
```


### 常见报错
#### docker报docker-runc not installed on system
```shell
ERROR: for jms_mysql  Cannot start service mysql: shim error: docker-runc not installed on system
ERROR: for redis  Cannot start service redis: shim error: docker-runc not installed on system
ERROR: for mysql  Cannot start service mysql: shim error: docker-runc not installed on system
```
解决：
> cd /usr/libexec/docker/
> ln -s docker-runc-current docker-runc

#### docker报
```shell
ERROR: for koko  Cannot start service koko: driver failed programming external connectivity on endpoint jms_koko (984f8ff1c9be861d8daec20312640ae8bac1e88260d5c36edbcec502ed1bd070): exec: "docker-proxy": executable file not found in $PATH
```
解决：
>  cd /usr/libexec/docker/
> ln -s docker-proxy-current /usr/bin/proxy

##### docker报
```shell
/usr/bin/docker-current: Error response from daemon: driver failed programming external connectivity on endpoint vouch-proxy (b767d11f419c99b665272749461524dd8374b27f6d59780c67fd92b12d3896c4): exec: "docker-proxy": executable file not found in $PATH.
```
解决：
> cd /usr/libexec/docker/
> ln -s docker-proxy-current docker-proxy
