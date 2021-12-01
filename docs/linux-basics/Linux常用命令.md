### 查IP

```bash
curl ip.cn
ip.cn
ipinfo.io
cip.cc
ifconfig.me
myip.ipip.net
#查询指定地址 curl cip.cc/10.0.1.1  
curl cip.cc
curl ifconfig.me
curl ifconfig.me/all
curl www.pubyun.com/dyndns/getip
curl members.3322.org/dyndns/getip
```


### 挂载Nas

```bash
#yum install nfs-utils
yum install cifs-utils
mount -t cifs -o username=gourds,password=xxxx,vers=1.0  //pan.share.net/Public/ /mnt/pan
```


### 时间同步及设置

```bash
#www.pool.ntp.org
ntpdate -u cn.pool.ntp.org
#yum install ntpdate -y
*/5 * * * * ntpdate cn.ntp.org.cn && hwclock  --systohc
#时间设置
date -s "20100405 14:31:00"
```


### sudo执行shell

```bash
sudo sh -c "echo -e 'LoadPlugin memory\n<Plugin memory>\n\tValuesPercentage true\n</Plugin>' > /etc/collectd.d/memory.conf"
sudo sh -c "find /opt/supervisor/log/ -type f -mtime +30 -name "*.log*" -print0 |xargs -0 rm -f {}"
#删除文件过多时的问题
sudo find /opt/supervisor/log/ -type f -mtime +30 -name "*.log*" -print0 | xargs -0 -n10 rm -f {}
```


### utf-8的bom问题

问题`Bourne-Again shell script text executable, UTF-8 Unicode text, with CRLF line terminators`

```bash
file UploadCDN.sh
save_text: ASCII text, with very long lines
#utf8的bom头由\xEF,\xBB,\xBF组成
xxd filename | head -n1
#00000000: efbb bfe6 b58b e8af 95e6 9687 e4bb b60a
sed -i 's/^\xEF\xBB\xBF//g' filename
#批量去除文件夹中utf8文件中的bom头
grep -r -i -l $'^\xEF\xBB\xBF' . | xargs sed -i 's/^\xEF\xBB\xBF//g'
```


### 修改系统ulimit参数

```bash
#在线修改
prlimit --pid 28176 |grep NOFILE
prlimit --pid 28176 --nofile=50000
#修改文件
cat /etc/security/limits.d/max.conf
root soft nofile 500000
root hard nofile 500000
* soft nofile 500000
* hard nofile 500000
```


### 传输限速

```bash
trickle -s -u 30720 scp xxx user@xxx:/path
```



### ls

```
ls -d /dir/*/
```



### 批量修改后缀

```bash
#1
rename ".txt" "" *
#2
find . -name "*.txt" | awk '{new=gensub(".txt","",1);system("mv "$0" "new)}'
```


### 查找含文本文件并替换

```bash
find ./config/ -type f |xargs grep "ssy.bj.lcc100.com" |awk -F: '{print $1}' |xargs sed -i 's@ssy.bj.lcc100.com@ssy.shec.edu.cn@g'
```


### 查找文件匹配字符串注释

```bash
find /etc/cron.d/ -type f | grep redisstorage |xargs sed -i "s/^.*redisstorage-userinfo.*/#&/g"
```


### 清理Cache

```
[root@host ~]# sync
[root@host-app2 ~]# echo 3 > /proc/sys/vm/drop_caches
```


### Passwd设置

```bash
echo "t<uv6L9$!!" | passwd --stdin custom
#useradd  -p `openssl   passwd   -1  -salt  '盐'  密码` 用户名
```



### SSH端口隧道

```bash
ssh -L 8080:192.168.164.14:8080 root@218.77.121.90
```


### SSH远程执行命令

```bash
ssh -p 22 user@host "command"  #双引号command里的变量等特殊命令字符会在本地解析，此时命令中不需要再本地解析引用的变量或特殊字符就需要在期前面加上转义符“\”
ssh -i authkey -p 22 user@host 'command' #单引号，不解析
```


### rm删除特殊命令

```bash
#删除带-的命令
rm -rf ./-yourstr
rm -- -yourstr
#删除带特殊字符的
rm >
rm "*"
```


### 用户附加群组

```bash
usermod -a -G group user

#加sudo
echo ec2-user ALL=(ALL) NOPASSWD: ALL  >> /etc/sudoers
```


### sshpass带密码登录

```bash
/usr/local/bin/sshpass -p 'your_pass' ssh  -o StrictHostKeychecking=no  root@1.1.1.1 -p22
```


### 加密压缩解压缩

```bash
tar -czvf - file | openssl des3 -salt -k passw0rd -out /path/to/file.tar.gz
#解密解压
openssl des3 -d -k passw0rd -salt -in /path/to/file.tar.gz | tar xvf -
```


### 常用JMS用户权限模板

```
ALL,!/bin/bash,!/bin/tcsh,!/bin/su,!/usr/bin/passwd,!/usr/bin/passwd root,!/bin/vim /etc/sudoers,!/usr/bin/vim /etc/sudoers,!/usr/sbin/visudo,!/usr/bin/sudo -i,!/bin/bi /etc/ssh/*,!/bin/chmod 777 /etc/*,!/bin/chmod 777 *,!/bin/chmod 777,!/bin/chmod -R 777 *,!/bin/rm /*,!/bin/rm /,!/bin/rm -rf /,!/bin/rm -rf /*,!/bin/rm /etc,!/bin/rm -r /etc,!/bin/rm -rf /etc,!/bin/rm /etc/*,!/bin/rm -r /etc/*,!/bin/rm -rf /etc/*,!/bin/rm /root,!/bin/rm -r /root,!/bin/rm -rf /root,!/bin/rm /root/*,!/bin/rm -r /root/*,!/bin/rm -rf /root/*,!/bin/rm /bin,!/bin/rm -r /bin,!/bin/rm -rf /bin,!/bin/rm /bin/*,!/bin/rm -r /bin/*,!/bin/rm -rf /bin/*
```


### 临时加swap

```bash
dd if=/dev/zero of=/home/swapfile bs=1M count=1024
mkswap /home/swapfile
swapon /home/swapfile #不重启生效
/home/swapfile_10G      swap                    swap    defaults        0 0#开机挂载vi /etc/fstab
```


### 初始化磁盘划分

| 挂载点 | 硬盘分区  | 推荐大小     |
| ------ | --------- | ------------ |
| /      | /dev/hda1 | 15G          |
| /boot  | /dev/hda2 | 300M         |
| swap   | /dev/hda3 | 2G           |
| /home  | /dev/hda4 | 剩余所有空间 |



### 查看进程文件描述符使用

```bash
#查看单个进程
ls -lR /proc/13973/fd/ |grep "^l" | wc -l

#查看所有进程打开的文件描述符并排序
lsof -n |awk '{print $2}'|sort|uniq -c |sort -nr
```



### cp

| 参数                | 描述                                                   |
| ------------------- | ------------------------------------------------------ |
| -a, --archive       | same as -dR --preserve=all                             |
| --backup[=CONTROL]  | 如果目标文件存在则备份                                 |
| -R, -r, --recursive | 递归                                                   |
| -v, --verbose       | 显示过程                                               |
| -x                  | 只拷贝本文件系统                                       |
| -u, --update        | 源文件比目标文件新或目标文件不存在时更新，可做断点续传 |

```bash
#只拷贝存在于本文件系统的文件，并保留文件的元信息
cp -avx  /dir1  /dir2
#支持断点续传
cp -auvx /dir1  /dir2
```



### 网络设置

> systemctl start NetworkManager
> nmcli c show

>


> NAME       UUID                                  TYPE            DEVICE

> enp0s31f6  ab24d2da-0abe-48d6-8b3e-a26d7c46e55c  802-3-ethernet  enp0s31f6

> docker0    1b8b44e5-e65c-4358-b82b-e6ff94042643  bridge          docker0

> virbr0     48fc8c6f-acc4-4222-9a1b-2587e8d55c04  bridge          virbr0

```shell
#vi /etc/sysconfig/network-scripts/ifcfg-eth0
TYPE=Ethernet  //网络类型：Ethernet以太网类型
BOOTPROTO=none  //代理方式：关闭状态
BROWSER_ONLY=  //仅使用浏览器: 否
BOOTPROTO=none //引导协议：dhcp、static、none（不指定）
DEFROUTE=yes    //默认路由：启动
IPV4_FAILURE_FATAL=yes  //启用ipv4错误检查功能
IPV6INIT=no   //不启用ipv6地址
IPV6_AUTOCONF=no  //自动配置ipv6地址：关闭
IPV6_DEFROUTE=no  //不启用ipv6错误检查功能
IPV6_ADDR_GEN_MODE=stable-privacy  //ipv6地址生成模型
NAME=eth0    //网卡物理设备名称
UUID=ab24d2da-0abe-48d6-8b3e-a26d7c46e55c  //网卡唯一识别码
DEVICE=eth0  //网卡设备名称，必须和NAME一样
ONBOOT=yes   //系统启动时是否启动该网卡：启动
IPADDR=10.0.1.8  //IP地址
PREFIX=24    //子网掩码
GATEWAY=10.0.1.1  //网关
DNS1=202.106.0.20  //DNS
LAST_CONNECT=1484704230
```



### **关闭SElinux**

```bash
getenforce

#临时关闭
setenforce 0
#永久关闭
sed -i "s/^SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
```



### 生成pem

```bash
#https://developers.yubico.com/PIV/Guides/Generating_keys_using_OpenSSL.html
#https://gist.github.com/mingfang/4aba327add0807fa5e7f
ssh-keygen -t rsa -b 4096 -m PEM
openssl rsa -in id_rsa -outform pem > id_rsa.pem
```



### **Git Clone指定key**

```bash
ssh-agent bash -c 'ssh-add /somewhere/yourkey; git clone git@github.com:user/project.git'

#m2
GIT_SSH_COMMAND='ssh -i private_key_file -o IdentitiesOnly=yes' git clone user@host:repo.git
```
