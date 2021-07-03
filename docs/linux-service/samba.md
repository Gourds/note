**概述：** 文件共享服务器，适合做公司的共享，对win友好
### 常用指令
#### Linux下挂载samba
```bash
#guest使用 -o guest
mount -t cifs -o username=user,password=pass,vers=1.0  //1.1.1.1/Public/ /mnt/pan
```


### 快速启动（使用docker）
> 镜像地址：[https://hub.docker.com/r/dperson/samba/](https://hub.docker.com/r/dperson/samba/)

```bash
docker run -it  --name samba -p 139:139 -p 445:445  \
-v /data/samba/samba_data:/mount \
-d docker.io/dperson/samba \
-u "qa;111" \
-u "design;222" \
-u "market;333" \
-u "dev;444" \
-u "ops;555" \
-p \
-s "Public;/mount/Shared;yes;no;yes;all;none;all" \
-s "Market;/mount/Market;yes;no;no;market,design;ops;market,design" \
-s "Design;/mount/Design;yes;no;no;design,market;ops;design,market" \
-s "DEV;/mount/DEV;no;no;no;dev;dev;dev" \
-s "OPS;/mount/OPS;no;no;no;ops;ops;ops" \
-s "QA;/mount/QA;yes;no;no;qa;qa;qa" \
-g "log level = 2"
```
