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

### 新版本安装

```
yum install yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum-config-manager --enable docker-ce-nightly
yum --showduplicates list docker-ce | expand
yum install docker-ce docker-ce-cli containerd.io
```

### 安装docker-compose

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod a+x /usr/local/bin/docker-compose
echo 'export PATH=/usr/local/bin:$PATH' > /etc/profile.d/usr.sh
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
