### 使用lrzsz传输文件
> **Tips:** 
> - 适合中小文件
> 
**使用说明：** 

> - sz：将选定的文件发送（send）到本地机器

> - rz：运行该命令会弹出一个文件选择窗口，从本地选择文件上传到服务器(receive)


#### 源码安装
```bash
pkg_version='lrzsz-0.12.20.tar.gz'
wget https://ohse.de/uwe/releases/${pkg_version}
tar xvf ${pkg_version}
cd ${pkg_version%\.tar\.gz}
./configure --prefix=/usr/local/lrzsz
make && make install
ln -s /usr/local/lrzsz/bin/lrz /usr/bin/rz
ln -s /usr/local/lrzsz/bin/lsz /usr/bin/sz
```
#### yum安装
```bash
yum install -y lrzsz
#配合xshell可直接拖拽文件到服务器
```
### 使用scp传输文件
#### 常用命令示例
```bash
scp local_file remote_username@remote_ip:remote_file 
scp -r local_folder remote_username@remote_ip:remote_folder
#从本地复制到远程服务器
scp root@192.168.1.2:/opt/xxx.tar.gz /opt/soft/
```


