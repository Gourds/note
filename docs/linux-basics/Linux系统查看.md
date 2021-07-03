## RELEASE查看
#### CentOS版本
```bash
cat /etc/redhat-release
 rpm -q centos-release
 cat /etc/issue
```
#### CentOS内核版本及架构
```bash
uname -r
uname -a
cat /proc/version
```
## CPU查看
#### 查看cpu详细信息
```bash
cat /proc/cpuinfo
```
#### 查看占用cpu的应用
```bash
ps aux |head -1;ps aux|sort -k3nr |head -10
#查看CPU占用最多的10个进程
```
#### 查看cpu型号
```bash
cat /proc/cpuinfo | grep name |cut -f2 -d: |uniq -c
```
#### 查看cpu架构
```bash
getconf LONG_BIT
#是查看当前是32还是64，当前是32也不代表这个CPU不支持64bit
```
#### 查看内核版本
```bash
uname -r
uname -a
cat /proc/version
```
#### 查看cpu是否支持64bit
```bash
cat /proc/cpuinfo |grep flags | grep "lm" |wc -l
#结果大于1即为支持，lm为long mode，意思是支持64bit
```
#### 查看物理cpu个数
```bash
cat /proc/cpuinfo |grep "physical id" |sort |uniq |wc -l
# 查看每个物理CPU中core的个数(即核数)
cat /proc/cpuinfo| grep "cpu cores"| uniq
```
#### 查看逻辑cpu个数
```bash
cat /proc/cpuinfo |grep "processor"|wc -l
#正常说的cpu核心数=物理核心数 X CPU核心数， 如果计算的cpu核心数是逻辑核心数的一半说明这款cpu支持并开启了ht（超线程技术）功能
```
#### 查看cpu核心数
```bash
cat /proc/cpuinfo |grep "cores" |wc -l
```
## MEM查看
#### 查看占用内存最多的进程
```bash
ps aux | sort -k4nr |head -10
free -m #查看系统内存占用
```
#### 查看分别占用最多的VSZ和RSS
> VSZ为进程申请的内存叫做虚拟内存
> RSS为进程占用的内存叫做使用的内存

```bash
#VSZ
ps aux | sort -k5nr |head -10
#RSS
ps aux | sort -k6nr |head -10
```
#### 查看某个进程（PID）占用的详细内存
```bash
pmap PID
#pmap 3345 |tail -1
```
## DISK查看
#### 查看磁盘使用-du命令
```bash
#du -sh /path/*
-a或-all  显示目录中个别文件的大小。   
-b或-bytes  显示目录或文件大小时，以byte为单位。   
-c或--total  除了显示个别目录或文件的大小外，同时也显示所有目录或文件的总和。 
-k或--kilobytes  以KB(1024bytes)为单位输出。
-m或--megabytes  以MB为单位输出。   
-s或--summarize  仅显示总计，只列出最后加总的值。
-h或--human-readable  以K，M，G为单位，提高信息的可读性。
-x或--one-file-xystem  以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过。 
-L<符号链接>或--dereference<符号链接> 显示选项中所指定符号链接的源文件大小。   
-S或--separate-dirs   显示个别目录的大小时，并不含其子目录的大小。 
-X<文件>或--exclude-from=<文件>  在<文件>指定目录或文件。   
--exclude=<目录或文件>         略过指定的目录或文件。    
-D或--dereference-args   显示指定符号链接的源文件大小。   
-H或--si  与-h参数相同，但是K，M，G是以1000为换算单位。   
-l或--count-links   重复计算硬件链接的文件。  
```
#### 查看磁盘空闲-df命令
```bash
#df -hl
必要参数：
-a 全部文件系统列表
-h 方便阅读方式显示
-H 等于“-h”，但是计算式，1K=1000，而不是1K=1024
-i 显示inode信息
-k 区块为1024字节
-l 只显示本地文件系统
-m 区块为1048576字节
--no-sync 忽略 sync 命令
-P 输出格式为POSIX
--sync 在取得磁盘信息前，先执行sync命令
-T 文件系统类型
选择参数：
--block-size=<区块大小> 指定区块大小
-t<文件系统类型> 只显示选定文件系统的磁盘信息
-x<文件系统类型> 不显示选定文件系统的磁盘信息
--help 显示帮助信息
--version 显示版本信息
```
#### fdisk命令
```bash
fdisk -l#查看硬盘信息
#硬盘分区
fdisk /dev/xxx
   p、打印分区表。
   n、新建一个新分区。
   d、删除一个分区。
   q、退出不保存。
   w、把分区写进分区表，保存并退出。
#格式化挂载使用分区
mkfs.ext3 /dev/hdd1
mount /dev/hdd1 /hdd1
partprobe /dev/sdb
mkfs.ext4 /dev/sdb1
ls -l /dev/disk/by-uuid/
vim /etc/fstab
#UUID=xxx /                       ext4    defaults        1 1
```
#### 常见磁盘基础知识
> ### 机械硬盘还有两个物理指标：寻道时间 和 旋转延迟 
> - 7200 转/分的STAT硬盘平均物理寻道时间是9ms，平均旋转延迟大约 4.17ms

> - 10000转/分的STAT硬盘平均物理寻道时间是6ms ，平均旋转延迟大约 3ms

> - 15000转/分的SAS硬盘平均物理寻道时间是4ms，平均旋转延迟大约 2ms

## 时间时区查看
#### 查看当前时间
```bash
date
```
#### 查看当前时区
```bash
date +"%Z %z"
timedatectl
grep ZONE /etc/sysconfig/clock #适用RHEL/CentOS/Fedora
cat /etc/timezone
```
