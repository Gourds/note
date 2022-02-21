### 网络排错常用命令

#### vmstat
```
vmstat -t 3
```

```
procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu------          ---timestamp---
 r  b     swpd   free   buff    cache       si   so    bi   bo   in   cs us sy id wa st
 0  0      0    3041180 1210148 19739664    0    0     0    84    0    0  8  5 87  0  0    2021-09-23 10:26:25 UTC
 2  0      0    3045704 1210148 19739664    0    0     0    11 14911 3806  5  6 89  0  0   2021-09-23 10:26:28 UTC

#
Procs
    r: The number of processes waiting for run time.
    b: The number of processes in uninterruptible sleep.
Memory
    swpd: the amount of virtual memory used.
    free: the amount of idle memory.
    buff: the amount of memory used as buffers.
    cache: the amount of memory used as cache.
    inact: the amount of inactive memory. (-a option)
    active: the amount of active memory. (-a option)
Swap
    si: Amount of memory swapped in from disk (/s).
    so: Amount of memory swapped to disk (/s).
IO
    bi: Blocks received from a block device (blocks/s).
    bo: Blocks sent to a block device (blocks/s).
System
    in: The number of interrupts per second, including the clock.
    cs: The number of context switches per second.
CPU
    These are percentages of total CPU time.
    us: Time spent running non-kernel code. (user time, including nice time)
    sy: Time spent running kernel code. (system time)
    id: Time spent idle. Prior to Linux 2.5.41, this includes IO-wait time.
    wa: Time spent waiting for IO. Prior to Linux 2.5.41, included in idle.
    st: Time stolen from a virtual machine. Prior to Linux 2.6.11, unknown.
```

#### iostat
- 查看的是块设备（block Device）级别的信息。在OS内核内部，一般不会记录文件缓存等OS文件系统级别的操作，所以OS上的应用程序看到的性能信息与iostat级别的性能信息之间有差异
- 可以知道磁盘的繁忙程度。

```bash
#可以知道响应时间和各种队列长度
iostat -x

iostat -x -t 3
```

```
avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.17    0.00    0.15    0.00    0.00   99.67

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
vda               0.00     0.90    0.01    0.89     0.24    11.89    26.81     0.00    1.44    0.51    1.45   0.09   0.01

#report
Device
    rrqm/s: The number of read requests merged per second that were queued to the device.
    wrqm/s: The number of write requests merged per second that were queued to the device
    r/s: The number (after merges) of read requests completed per second for the device
    w/s: The number (after merges) of write requests completed per second for the device
    avgrq-sz: The average size (in sectors) of the requests that were issued to the device.
    avgqu-sz: The average queue length of the requests that were issued to the device.
    await: The average time (in milliseconds) for I/O requests issued to the device to be served. This includes the time spent by the requests in queue and  the  time  spent servicing them.
    r_await: The  average  time (in milliseconds) for read requests issued to the device to be served. This includes the time spent by the requests in queue and the time spent servicing them.
    w_await: The average time (in milliseconds) for write requests issued to the device to be served. This includes the time spent by the requests in queue and the time  spent servicing them.
    svctm: The average service time (in milliseconds) for I/O requests that were issued to the device. Warning! Do not trust this field any more.  This field will be removed in a future sysstat version.
    %util: Percentage of elapsed time during which I/O requests were issued to the device (bandwidth utilization for the device). Device saturation occurs when this value is close to 100%.
```

- active指的是从OS角度看已经向磁盘发送完毕，wait指的是尚未发送.当存储接近临界值时，首先active队列会增加，响应时间也会变慢，接着wait的队列长度也会增加，响应也会变慢
- svctm称为服务时间，其实可以当成响应时间。已弃用
- 一般未命中缓存的情况下1次IO响应差不多是几毫秒。如果响应话费10几毫秒，可以怀疑响是否有问题了

#### ping
##### 基本用法
```bash
ping [选项] [主机]
#参数
-d 使用Socket的SO_DEBUG功能。
-f  极限检测。大量且快速地送网络封包给一台机器，看它的回应。
-n 只输出数值。
-q 不显示任何传送封包的信息，只显示最后的结果。
-r 忽略普通的Routing Table，直接将数据包送到远端主机上。通常是查看本机的网络接口是否有问题。
-R 记录路由过程。
-v 详细显示指令的执行过程。
<p>-c 数目：在发送指定数目的包后停止。
-i 秒数：设定间隔几秒送一个网络封包给一台机器，预设值是一秒送一次。
-I 网络界面：使用指定的网络界面送出数据包。
-l 前置载入：设置在送出要求信息之前，先行发出的数据包。
-p 范本样式：设置填满数据包的范本样式。
-s 字节数：指定发送的数据字节数，预设值是56，加上8字节的ICMP头，一共是64ICMP数据字节。
-t 存活数值：设置存活数值TTL的大小。
```
##### 常用示例
```bash
#指定源地址 win 使用-l -n
ping -a 192.168.0.1 8.8.8.8
#指定数据包大小及数量
ping -s 1500 -c 100 8.8.8.8
#禁止分片
ping -s 1472 -f 8.8.8.8
#快速查找MAC及ARP
arp -a
display mac-address |include MAC
display arp |include MAC/IP
```

---

#### telnet
##### 基本用法
```bash
#telnet 192.168.1.1 80
#选项
-8：允许使用8位字符资料，包括输入与输出；
-a：尝试自动登入远端系统；
-b<主机别名>：使用别名指定远端主机名称；
-c：不读取用户专属目录里的.telnetrc文件；
-d：启动排错模式；
-e<脱离字符>：设置脱离字符；
-E：滤除脱离字符；
-f：此参数的效果和指定"-F"参数相同；
-F：使用Kerberos V5认证时，加上此参数可把本地主机的认证数据上传到远端主机；
-k<域名>：使用Kerberos认证时，加上此参数让远端主机采用指定的领域名，而非该主机的域名；
-K：不自动登入远端主机；
-l<用户名称>：指定要登入远端主机的用户名称；
-L：允许输出8位字符资料；
-n<记录文件>：指定文件记录相关信息；
-r：使用类似rlogin指令的用户界面；
-S<服务类型>：设置telnet连线所需的ip TOS信息；
-x：假设主机有支持数据加密的功能，就使用它；
-X<认证形态>：关闭指定的认证形态。
```

---

#### nmap
##### 扫描主机端口
```bash
nmap IP/Hostname
#nmap 172.168.1.*
#nmap 172.168.2.1 172.168.2.2
#nmap 172.168.2.1，2
#nmap 172.168.1-2
#nmap -iL hostlist.txt
```
##### 查看主机TCP端口
```bash
nmap -p 80 172.168.1.1
```
##### 查看主机防火墙
```bash
nmap -sA 172.168.1.1
```

---

#### nc
##### 检测端口联通性
```bash
#检测TCP端口
nc -vz -w2 192.168.1.1 22
#检测UDP端口
nc -uz -w2 192.168.1.1 53

1.临时监听TCP端口
nc -l port
2.永久监听TCP端口
nc -lk port
3.临时监听UDP
nc -lu port
4.永久监听UDP
nc -luk port
```
##### 参数说明
```bash
-g<网关> 设置路由器跃程通信网关，最多可设置8个。
-G<指向器数目> 设置来源路由指向器，其数值为4的倍数。
-h 在线帮助。
-i<延迟秒数> 设置时间间隔，以便传送信息及扫描通信端口。
-l 使用监听模式，管控传入的资料。
-n 直接使用IP地址，而不通过域名服务器。
-o<输出文件> 指定文件名称，把往来传输的数据以16进制字码倾倒成该文件保存。
-p<通信端口> 设置本地主机使用的通信端口。
-r 乱数指定本地与远端主机的通信端口。
-s<来源位址> 设置本地主机送出数据包的IP地址。
-u 使用UDP传输协议。
-v 显示指令执行过程。
-w<超时秒数> 设置等待连线的时间。
-z 使用0输入/输出模式，只在扫描通信端口时使用。
```

---

#### traceroute
##### 查看路由
```bash
traceroute IP/hostname
#配合ip138排错
#默认向每个网关发送三个包，所以默认每一跳后面有三个时间，默认是-q3
```
##### 命令选项
```bash
-d：使用Socket层级的排错功能；
-f<存活数值>：设置第一个检测数据包的存活数值TTL的大小；
-F：设置勿离断位；
-g<网关>：设置来源路由网关，最多可设置8个；
-i<网络界面>：使用指定的网络界面送出数据包；
-I：使用ICMP回应取代UDP资料信息；
-m<存活数值>：设置检测数据包的最大存活数值TTL的大小；
-n：直接使用IP地址而非主机名称；
-p<通信端口>：设置UDP传输协议的通信端口；
-r：忽略普通的Routing Table，直接将数据包送到远端主机上。
-s<来源地址>：设置本地主机送出数据包的IP地址；
-t<服务类型>：设置检测数据包的TOS数值；
-v：详细显示指令的执行过程；
-w<超时秒数>：设置等待远端主机回报的时间；
-x：开启或关闭数据包的正确性检验。 参数
```

---

#### mtr
##### 基本用法
```bash
#mtr -c 400 -i 0.1  -n -r 8.8.8.8
#mtr IP/hostname
#选项
mtr -h  #提供帮助命令
mtr -v  #显示mtr的版本信息
mtr -r  #已报告模式显示
mtr -s  #用来指定ping数据包的大小
mtr --no-dns  #不对IP地址做域名解析
mtr -a  #来设置发送数据包的IP地址 这个对一个主机由多个IP地址是有用的
mtr -i  #使用这个参数来设置ICMP返回之间的要求默认是1秒
mtr -4  #IPv4
mtr -6  #IPv6
```
##### 命令输出说明
| Host | Loss | Snt | Last | Avg | Best | Wrst | StDev |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 172.17.0.1 | 0.0% | 608 | 0.7 | 0.6 | 0.6 | 3.8 | 0.2 |
| 地址 | 丢包率 | 每秒发送数据包量 | 最近的ping值 | 平均ping | 最好ping | 最差ping |
标准偏差 |

##### 命令安装
```bash
#mac os install
sudo chown -R $(whoami):admin /usr/local
brew install mtr
brew link mtr
```

---

### 抓包排错
#### tcpdump
> 参考：
> - [http://linuxwiki.github.io/NetTools/tcpdump.html](http://linuxwiki.github.io/NetTools/tcpdump.html)
> - [https://www.cnblogs.com/losbyday/p/5851767.html](https://www.cnblogs.com/losbyday/p/5851767.html)

##### Ex1: 抓取指定主机
```bash
tcpdump -i eth1 host 192.168.1.1
#抓取所有经过eth1，目的或源地址为192.168.1.1的数据
tcpdump -i eth1 src host 192.168.1.1
#指定源地址
tcpdump -i eth1 dst host 192.168.1.1
#指定目的地址
```
##### Ex2： 抓取指定端口
```bash
tcpdump -i eth1 port 25
#抓取所有经过eth1，目的或源端口是25的网络数据
tcpdump -i eth1 src port 25
#指定源端口
tcpdump -i eth1 dst port 25
#指定目的端口
```
##### Ex3: 网络过滤
```bash
tcpdump -i eth1 net 192.168
tcpdump -i eth1 src net 192.168
tcpdump -i eth1 dst net 192.168
```
##### Ex4: 协议过滤
```bash
tcpdump -i eth1 arp
tcpdump -i eth1 ip
tcpdump -i eth1 tcp
tcpdump -i eth1 udp
tcpdump -i eth1 icmp
```
##### Ex5: 多条件过滤
```bash
tcpdump -i eth1 '((tcp) and (port 80) and ((dst host 192.168.1.254) or (dst host 192.168.1.200)))'
#抓取所有经过eth1，目的地址是192.168.1.254或192.168.1.200端口是80的TCP数据
tcpdump -i eth1 '((icmp) and ((ether dst host 00:01:02:03:04:05)))'
#抓取所有经过eth1，目标MAC地址是00:01:02:03:04:05的ICMP数据
tcpdump -i eth1 '((tcp) and ((dst net 192.168) and (not dst host 192.168.1.200)))'
#抓取所有经过eth1，目的网络是192.168，但目的主机不是192.168.1.200的TCP数据
#---表达式---
#非 : ! or "not" (去掉双引号)  
#且 : && or "and"  
#或 : || or "or"
```
##### 常用示例
```bash
#抓取获取唯一ip
tcpdump -nn -q ip -l | awk '{ ip = gensub(/([0-9]+.[0-9]+.[0-9]+.[0-9]+)(.*)/,"\\1","g",$3); if(!d[ip]) { print ip; d[ip]=1; fflush(stdout) } }'

#抓100个(-c)完整的(-s 0)数据包,不显示时间戳(-t)
tcpdump tcp -i eth1 -t -s 0 -c 100 and dst port 22 and src net 192.168.1.0/24 -w ./save.cap
#
#抓取指定网卡所有数据包并保存
tcpdump -i eth0 -w  save.cap
#抓取指定网卡从baidu发送来的所有数据
tcpdump -i eth0 src host baidu.com
#抓取指定网卡发送到baidu.com的所有数据
tcpdump -i eth0 dst host baidu.com
#抓取指定主机（即源或目标都匹配,可以是域名也可以是IP,还可以抓取两个地址之间的通信）
tcpdump host baidu.com
tcpdump host 114.114.114.114
tcpdump host host1 and host2
tcpdump host 114.114.114.114 and 8.8.8.8
tcpdump host 114.114.114.114 and \(8.8.8.8 or 8.8.4.4)
#抓取除google.com之外baidu相关的所有数据包
tcpdump ip host baidu.com and not google.com
#
#抓取主机baidu.com从TCP22端口接收或发送的数据包
tcpdump tcp port 22 and host baidu.com
tcpdump udp port 53 and host 114.114.114.114
#
#抓取TCP会话中的的开始和结束数据包, 并且数据包的源或目的不是本地网络上的主机
tcpdump 'tcp[tcpflags] & (tcp-syn|tcp-fin) != 0 and not src and dst net localnet'
#抓取所有源或目的端口是80,网络层协议为IPv4,并且含有数据,而不是SYN,FIN以及ACK-only等不含数据的数据包
tcpdump 'tcp port 80 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'
#抓取长度超过576字节, 并且网关地址是baidu.com的IP数据包
tcpdump 'gateway baidu.com and ip[2:2] > 576'
#抓取所有IP层广播或多播的数据包， 但不是物理以太网层的广播或多播数据报
tcpdump 'ether[0] & 1 = 0 and ip[16] >= 224'
#抓取除'echo request'或者'echo reply'类型以外的ICMP数据包
tcpdump 'icmp[icmptype] != icmp-echo and icmp[icmptype] != icmp-echoreply'
#
#抓取HTTP包(0x4745为"GET"前两个字母"GE",0x4854为"HTTP"前两个字母"HT")
tcpdump  -XvvennSs 0 -i eth0 tcp[20:2]=0x4745 or tcp[20:2]=0x4854
```

---

#### wireshark
##### Ex1: 分析ssh登陆 
```bash
#先使用Filter过滤ssh协议–>然后定位有问题的ssh连接–>然后使用frame过滤出有问题的行
frame.number>34&&frame.number<56
#检索出34到56行
```

---



### 系统及进程排错
#### ps
##### 常用选项
```bash
ps aux
ps -ef
ps -lA
ps -axjf
```
##### 选项手册
```bash
　　-a  显示所有终端机下执行的进程，除了阶段作业领导者之外。
　　 a  显示现行终端机下的所有进程，包括其他用户的进程。
　　-A  显示所有进程。
　　-c  显示CLS和PRI栏位。
　　 c  列出进程时，显示每个进程真正的指令名称，而不包含路径，参数或常驻服务的标示。
　　-C<指令名称> 　指定执行指令的名称，并列出该指令的进程的状况。
　　-d 　显示所有进程，但不包括阶段作业领导者的进程。
　　-e 　此参数的效果和指定"A"参数相同。
　　 e 　列出进程时，显示每个进程所使用的环境变量。
　　-f 　显示UID,PPIP,C与STIME栏位。
　　 f 　用ASCII字符显示树状结构，表达进程间的相互关系。
　　-g<群组名称> 　此参数的效果和指定"-G"参数相同，当亦能使用阶段作业领导者的名称来指定。
　　 g 　显示现行终端机下的所有进程，包括群组领导者的进程。
　　-G<群组识别码> 　列出属于该群组的进程的状况，也可使用群组名称来指定。
　　 h 　不显示标题列。
　　-H 　显示树状结构，表示进程间的相互关系。
　　-j或j 　采用工作控制的格式显示进程状况。
　　-l或l 　采用详细的格式来显示进程状况。
　　 L 　列出栏位的相关信息。
　　-m或m 　显示所有的执行绪。
　　 n 　以数字来表示USER和WCHAN栏位。
　　-N 　显示所有的进程，除了执行ps指令终端机下的进程之外。
　　-p<进程识别码> 　指定进程识别码，并列出该进程的状况。
　 　p<进程识别码> 　此参数的效果和指定"-p"参数相同，只在列表格式方面稍有差异。
　　 r 　只列出现行终端机正在执行中的进程。
　　-s<阶段作业> 　指定阶段作业的进程识别码，并列出隶属该阶段作业的进程的状况。
　 　s 　采用进程信号的格式显示进程状况。
　　 S 　列出进程时，包括已中断的子进程资料。
　　-t<终端机编号> 　指定终端机编号，并列出属于该终端机的进程的状况。
　　 t<终端机编号> 　此参数的效果和指定"-t"参数相同，只在列表格式方面稍有差异。
　　-T 　显示现行终端机下的所有进程。
　　-u<用户识别码> 　此参数的效果和指定"-U"参数相同。
　　 u 　以用户为主的格式来显示进程状况。
　　-U<用户识别码> 　列出属于该用户的进程的状况，也可使用用户名称来指定。
　　 U<用户名称> 　列出属于该用户的进程的状况。
　　 v 　采用虚拟内存的格式显示进程状况。
　　-V或V 　显示版本信息。
　　-w或w 　采用宽阔的格式来显示进程状况。　
　 　x 　显示所有进程，不以终端机来区分。
　　 X 　采用旧式的Linux i386登陆格式显示进程状况。
　　 -y 配合参数"-l"使用时，不显示F(flag)栏位，并以RSS栏位取代ADDR栏位
　　-<进程识别码> 　此参数的效果和指定"p"参数相同。
　　--cols<每列字符数> 　设置每列的最大字符数。
　　--columns<每列字符数> 　此参数的效果和指定"--cols"参数相同。
　　--cumulative 　此参数的效果和指定"S"参数相同。
　　--deselect 　此参数的效果和指定"-N"参数相同。
　　--forest 　此参数的效果和指定"f"参数相同。
　　--headers 　重复显示标题列。
　　--help 　在线帮助。
　　--info 　显示排错信息。
　　--lines<显示列数> 设置显示画面的列数。
　　--no-headers  此参数的效果和指定"h"参数相同，只在列表格式方面稍有差异。
　　--group<群组名称> 　此参数的效果和指定"-G"参数相同。
　　--Group<群组识别码> 　此参数的效果和指定"-G"参数相同。
　　--pid<进程识别码> 　此参数的效果和指定"-p"参数相同。
　　--rows<显示列数> 　此参数的效果和指定"--lines"参数相同。
　　--sid<阶段作业> 　此参数的效果和指定"-s"参数相同。
　　--tty<终端机编号> 　此参数的效果和指定"-t"参数相同。
　　--user<用户名称> 　此参数的效果和指定"-U"参数相同。
　　--User<用户识别码> 　此参数的效果和指定"-U"参数相同。
　　--version 　此参数的效果和指定"-V"参数相同。
　　--widty<每列字符数> 　此参数的效果和指定"-cols"参数相同。
```

---

#### top
##### 输出详解
![top_output_ex1.png](https://cdn.nlark.com/yuque/0/2020/png/2623495/1602313230809-8f90a29b-a4b3-46cb-b38c-eada258a3f20.png#align=left&display=inline&height=173&margin=%5Bobject%20Object%5D&name=top_output_ex1.png&originHeight=173&originWidth=877&size=246124&status=done&style=none&width=877)

| 12:57:32 up 63 days, 35 min | 当前时间12点57分，系统运行了63天35分 |
| --- | --- |
| 3 users | 当前登陆用户为3人 |
| load average:0.05,0.01,0.00 | 三个数值分别为1分钟、5分钟、15分钟系统的负载（即任务队列平均长度） |
| Tasks：140 total | 当前进程总数为140个 |
| 1 running，139 sleeping，0 stopped，0 zombie | 共有1个运行、139个睡眠、0个停止、0个僵尸进程 |
| Cpu(s): 0.0%us | 用户空间占用cpu百分比 |
| Cpu(s): 0.0%sy | 内核空间占用cpu百分比 |
| Cpu(s): 0.0%ni | 用户进程空间内改变过优先级的进程占用cpu百分比 |
| Cpu(s): 99.8%id | 空闲cpu占用百分比 |
| Cpu(s): 0.0%wa | IO等待占用CPU的百分比 |
| Cpu(s): 0.0%hi | 硬中断（Hardware IRQ）占用CPU的百分比 |
| Cpu(s): 0.0%si | 软中断（Software Interrupts）占用CPU的百分比 |
| Cpu(s): 0.0%st |  |
| Mem: total | 总共的内存大小 |
| Mem: used | 使用的内存大小 |
| Mem: free | 空闲内存大小 |
| Mem: buffers | 缓存的内存大小 |
| Swap: total | 总共的交换分区大小 |
| Swap: used | 使用的交换分区大小 |
| Swap: free | 空闲的交换分区大小 |
| Swap: cached | 缓冲的交换分区大小 |
| PID | 进程ID |
| USER | 进程所有者 |
| PR | 进程优先级 |
| NI | nice值。负值表示高优先级，正值表示低优先级 |
| VIRT | 进程使用的虚拟内存总量，单位Kb。VIRT=SWAP+RES |
| RES | 进程使用的未被换出的物理内存大小，单位kb。RES=CODE+DATA |
| SHR | 共享内存大小 |
| S | 进程状态。D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程 |
| %CPU | 上次更新到现在的cpu时间占用比 |
| %MEM | 进程使用的物理内存百分比 |
| TIME+ | 进程使用的cpu时间总计，单位1/100秒 |
| COMMAND | 进程名称（命令名） |

##### 命令交互(常用）
```bash
h 显示帮助画面，给出一些简短的命令总结说明
k 终止一个进程。
i 忽略闲置和僵死进程。这是一个开关式命令。
q 退出程序
r 重新安排一个进程的优先级别
S 切换到累计模式
s 改变两次刷新之间的延迟时间（单位为s），如果有小数，就换算成m s。输入0值则系统将不断刷新，默认值是5 s
f或者F 从当前显示中添加或者删除项目
o或者O 改变显示项目的顺序
l 切换显示平均负载和启动时间信息
m 切换显示内存信息
t 切换显示进程和CPU状态信息
c 切换显示命令名称和完整命令行
M 根据驻留内存大小进行排序
P 根据CPU使用百分比大小进行排序
T 根据时间/累计时间进行排序
W 将当前设置写入~/.toprc文件中
```

**特殊说明**

```
1. %CPU  --  CPU Usage
    The task's share of the elapsed CPU time since the last screen update, expressed as a percentage of total CPU time.

    In a true SMP environment, if a process is multi-threaded and top is not operating in Threads mode, amounts greater than 100% may be reported.  You toggle Threads mode with
    the `H' interactive command.

    Also for multi-processor environments, if Irix mode is Off, top will operate in Solaris mode where a task's cpu usage will be divided by the total number of CPUs.  You tog‐
    gle Irix/Solaris modes with the `I' interactive command.
```

---

#### strace
##### 常用示例
```bash
strace -c -p 2695
#
strace -tt -T -v -f -e trace=file -o /data/log/strace.log -s 1024 -p 23489
#-tt 在每行输出的前面，显示毫秒级别的时间
#-T 显示每次系统调用所花费的时间
#-v 对于某些相关调用，把完整的环境变量，文件stat结构等打出来。
#-f 跟踪目标进程，以及目标进程创建的所有子进程
#-e 控制要跟踪的事件和跟踪行为,比如指定要跟踪的系统调用名称
#-o 把strace的输出单独写到指定的文件
#-s 当系统调用的某个参数是字符串时，最多输出指定长度的内容，默认是32个字节
#-p 指定要跟踪的进程pid, 要同时跟踪多个pid, 重复多次-p选项即可
#-c 统计每一系统调用的所执行的时间,次数和出错的次数等.
strace -tt -T -f -e trace=file -o /data/log/strace.log -s 1024 ./nginx
#输出第一列是进程PID，接着是毫秒级别的时间（-tt），最后一行最后一列显示该调用花费的时间(-T)
#指定trace=file，可以看到跟进程有关的文件操作
#指定trace=process，表示只跟踪和进程管理相关的系统调用，比如fork/exec/exit_group
#指定trace=ipc，表示只跟中和京城相关的系统调用，比如shmget等
#指定trace=network，表示和网络通信相关的调用，比如socket/sendto/connect
#指定trace=desc，表示和文件描述符相关，比如write/read/select/epoll等
#指定trace=signal，表示信号发送和处理相关，比如kill/sigaction
#指定trace=all，表示统计所有的系统调用
```

---

#### sar
##### 常用示例
```bash
sar -o save.txt
#怀疑CPU存在瓶颈，可用 sar -u 和 sar -q 等来查看
#怀疑内存存在瓶颈，可用sar -B、sar -r 和 sar -W 等来查看
#怀疑I/O存在瓶颈，可用 sar -b、sar -u 和 sar -d 等来查看
#示例
sar
sar -u
#12:00:01 AM     CPU     %user     %nice   %system   %iowait    %steal     %idle
#12:10:01 AM     all      4.32      0.00      1.12      0.02      0.81     93.73
#解释（默认sar命令就是-u参数，查看cpu使用率
#%user 用户模式下消耗的CPU时间的比例
#%nice 通过nice改变了进程调度优先级的进程，在用户模式下消耗的CPU时间的比例
#%system 系统模式下消耗的CPU时间的比例；
#%iowait CPU等待磁盘I/O导致空闲状态消耗的时间比例。若 %iowait 的值过高，表示硬盘存在I/O瓶颈
#%steal 利用Xen等操作系统虚拟化技术，等待其它虚拟CPU计算占用的时间比例
#%idle CPU空闲时间比例；若 %idle 的值高但系统响应慢时，有可能是 CPU 等待分配内存，此时应加大内存容量。若 %idle 的值持续低于1，则系统的 CPU 处理能力相对较低，表明系统中最需要解决的资源是 CPU
sar -q
#12:00:01 AM   runq-sz  plist-sz   ldavg-1   ldavg-5  ldavg-15
#12:10:01 AM         0       383      0.48      0.35      0.37
#12:20:01 AM         0       383      0.29      0.20      0.26
#解释（查看平均负载）
#runq-sz：运行队列的长度（等待运行的进程数）
#plist-sz：进程列表中进程（processes）和线程（threads）的数量
#ldavg-1：最后1分钟的系统平均负载 ldavg-5：过去5分钟的系统平均负载
#ldavg-15：过去15分钟的系统平均负载
sar -r
#12:00:01 AM kbmemfree kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit
#12:10:01 AM    339184   7837320     95.85    185956   1653488  14140380    172.94
#12:20:01 AM    345020   7831484     95.78    185976   1657220  14145792    173.01
#解释（查看内存使用状况）
#kbmemfree：这个值和free命令中的free值基本一致,所以它不包括buffer和cache的空间.
#kbmemused：这个值和free命令中的used值基本一致,所以它包括buffer和cache的空间.
#物理内存使用率，这个值是kbmemused和内存总量(不包括swap)的一个百分比
#kbbuffers和kbcached：这两个值就是free命令中的buffer和cache
#kbcommit：保证当前系统所需要的内存,即为了确保不溢出而需要的内存(RAM+swap).
#%commit：这个值是kbcommit与内存总量(包括swap)的一个百分比
sar -W
#12:00:01 AM  pswpin/s pswpout/s
#12:10:01 AM      0.00      0.00
#12:20:01 AM      0.00      0.00
#解释（查看页面交换发生情况）
#pswpin/s：每秒系统换入的交换页面（swap page）数量
#pswpout/s：每秒系统换出的交换页面（swap page）数量
sar -d -p
#03:00:01 PM       DEV       tps  rd_sec/s  wr_sec/s  avgrq-sz  avgqu-sz     await     svctm     %util
#03:10:01 PM       sda      9.15    102.04    118.85     24.14      0.01      0.93      0.17      0.15
#03:10:01 PM       sdb     25.09     71.63    696.74     30.62      0.26     10.52      6.81     17.08
#解释（查看设备使用情况），-p参数可以显示磁盘等设备名称
#tcs: 每秒从物理磁盘I/O的次数.多个逻辑请求会被合并为一个I/O磁盘请求,一次传输的大小是不确定的
#rd_sec/s: 每秒读扇区的次数
#wr_sec/s: 每秒写扇区的次数.
#avgrq-sz: 平均每次设备I/O操作的数据大小(扇区).avgqu-sz 的值较低时，设备的利用率较高。
#avgqu-sz: 磁盘请求队列的平均长度.
#await: 从请求磁盘操作到系统完成处理,每次请求的平均消耗时间,包括请求队列等待时间,单位是毫秒(1秒=1000毫秒)
#svctm: 系统处理每次请求的平均时间,不包括在请求队列中消耗的时间
#%util: I/O请求占CPU的百分比,比率越大,说明越饱和.当%util的值接近 100% 时，表示设备带宽已经占满
```
##### 选项说明
```bash
#全选项
-A 汇总所有的报告
-a 报告文件读写使用情况
-B 报告附加的缓存的使用情况
-b 报告缓存的使用情况
-c 报告系统调用的使用情况
-d 报告磁盘的使用情况
-g 报告串口的使用情况
-h 报告关于buffer使用的统计数据
-m 报告IPC消息队列和信号量的使用情况
-n 报告命名cache的使用情况
-p 报告调页活动的使用情况
-q 报告运行队列和交换队列的平均长度
-R 报告进程的活动情况
-r 报告没有使用的内存页面和硬盘块
-u 报告CPU的利用率
-v 报告进程、i节点、文件和锁表状态
-w 报告系统交换活动状况
-y 报告TTY设备活动状况
```

---

#### vmstat
##### 基本使用
```bash
vmstat 2 1
#表示每两秒采集一次共采集1次
vmstat 2
#表示每两秒采集一次一直采集
```
##### 输出说明
| 输出 | 说明 |
| --- | --- |
| r | 在运行队列中等待的进程数，这个值超过CPU数目就可认为CPU达到瓶颈 |
| b | 在等待IO的进程数，进程阻塞程度 |
| swpd | 正在使用的swap大小，超过0就说明物理内存可能不足 |
| free | 空闲的物理内存的大小 |
| buff | 已使用的buff大小，对块设备的读写进行缓冲 |
| cache | 已使用cache的大小，文件系统的cache |
| si | 每秒从交换区写到内存的大小 |
| so | 每秒写入交换区的大小 |
| bi | 每秒读取的块数 |
| bo | 每秒写入的块数 |
| in | 每秒CPU的中断次数，包括时钟中断 |
| cs | 每秒上下文切换数 |
| us | 用户进程执行时间 |
| sy | 系统CPU时间，如果太高，表示系统调用时间长，例如是IO操作频繁 |
| id | 空闲 CPU时间 |
| wa | 等待IO时间 |


---

#### iostat
##### 输出说明
> Tips:
> - %util如果过高说明产生的I/O请求过多，磁盘负荷比较高


| 输出 | 说明 |
| --- | --- |
| Devices | 磁盘名称 |
| rrqm/s | 每秒进行 merge 的读操作数目。即 delta(rmerge)/s |
| wrqm/s | 每秒进行 merge 的写操作数目。即 delta(wmerge)/s |
| r/s | 每秒完成的读 I/O 设备次数。即 delta(rio)/s |
| w/s | 每秒完成的写 I/O 设备次数。即 delta(wio)/s |
| rsec/s | 每秒读扇区数。即 delta(rsect)/s |
| wsec/s | 每秒写扇区数。即 delta(wsect)/s |
| rkB/s | 每秒读K字节数。是 rsect/s 的一半，因为每扇区大小为512字节 |
| wkB/s | 每秒写K字节数。是 wsect/s 的一半 |
| avgrq-sz | 平均每次设备I/O操作的数据大小 (扇区)。即 delta(rsect+wsect)/delta(rio+wio) |
| avgqu-sz | 平均I/O队列长度。即 delta(aveq)/s/1000 (因为aveq的单位为毫秒) |
| await | 平均每次设备I/O操作的等待时间 (毫秒)。即 delta(ruse+wuse)/delta(rio+wio) |
| svctm | 平均每次设备I/O操作的服务时间 (毫秒)。即 delta(use)/delta(rio+wio) |
| %util | 一秒中有百分之多少的时间用于 I/O 操作，或者说一秒中有多少时间 I/O 队列是非空的 |


---

#### netstat
##### 常用命令组合
```bash
netstat -lntp
#列出在监听状态的TCP连接并不显示别名
netstat -lnup
#列出在监听状态的UDP连接并不显示别名
netstat -r
#显示核心路由信息
```
##### 常用统计
```bash
netstat -nat | grep "192.168.1.15:22" |awk '{print $5}'|awk -F: '{print $1}'|sort|uniq -c|sort -nr|head -20
#查看连接某服务最多的前20个IP地址
netstat -nat |awk '{print $6}'|sort|uniq -c
#查看所有TCP连接处于各个状态的个数
```
##### 常用选项说明
```bash
-a (all)显示所有选项，默认不显示LISTEN相关
-t (tcp)仅显示tcp相关选项
-u (udp)仅显示udp相关选项
-n 拒绝显示别名，能显示数字的全部转化成数字。
-l 仅列出有在 Listen (监听) 的服務状态
-p 显示建立相关链接的程序名
-r 显示路由信息，路由表
-e 显示扩展信息，例如uid等
-s 按各个协议进行统计
-c 每隔一个固定时间，执行该netstat命令。
-o timers
```

---

#### nmon
> **用途** ：以交互方式显示本地系统统计信息并以记录方式记录系统统计信息。
> - [https://yq.aliyun.com/articles/540541](https://yq.aliyun.com/articles/540541)

```bash
#Install
yum install nmon
```
##### 交互模式
```bash
#直接nmon进入即可，进入后可根据-h帮助信息查看对应的指标
nmon [ -h ]
```
##### 记录模式
```bash
#显示和记录本地系统信息.如果指定 -F、-f、-X、-x 和 -Z 标志中的任何一个，那么 nmon 命令处于记录方式.在记录模式下，此命令会生成nmon文件，可以通过该打开这些文件直接进行查看。在记录期间，nmon 工具会与 shell 断开连接，以确保该命令即使在您注销的情况下仍然继续运行。
nmon [ -f | -F filename | -x | -X | -z ] [ -r < runname > ] [ -t | -T | -Y ] [ -s seconds ] [ -c number ] [ -w number ] [ -l dpl ] [ -d ] [ -g filename ] [ -kdisklist ] [ -C <process1:process2:..:processN > ] [ -G ] [ -K ] [ -o outputpath ] [ -D ] [ -E ] [ -J ] [ -V ] [ -P ] [ -M ] [ -N ] [ -W ] [ -S ] [ -^ ] [ -O ] [ -L ] [ -I percent ] [ -A ] [ -m < dir > ] [ -Z priority ]
```
##### 进程视图模式
> 要显示此视图，请按 **t** 或 **v** 键

##### 使用示例
```bash
#Ex1：要在两个小时的时间段内在当前目录中生成 nmon 记录，每 30 秒捕获一次数据
nmon -f -s 30 -c 240
#Ex2: 要在 nmon 命令启动后立即显示内存和处理器统计信息
export NMON=mc
#Ex3: 要在 20 秒的时间段内运行 nmon 命令并且屏幕每 10 秒刷新一次
nmon -c 10 -s 2
```

---

#### curl
> Tips:
> - [http://man.linuxde.net/curl](http://man.linuxde.net/curl)

##### 常用示例
```bash
#Ex0: 带用户名密码请求
curl -u username:password http://example.com
#Ex1: 指定命令和头信息进行请求
curl -X GET -H "Authorization: Bearer token" -i  "https://your/uri"
#Ex2: 不显示统计信息进行请求（-s选项）
curl -X GET -s -i "http://your/uri"
```

---

#### perf
> Tips:
> - [https://ywnz.com/linux/perf/](https://ywnz.com/linux/perf/)

##### 常用示例
```bash
yum install perf
perf top -e cycles:k     #显示内核和模块中，消耗最多CPU周期的函数
perf top -e kmem:kmem_cache_alloc     #显示分配高速缓存最多的函数
perf top -G         #得到调用关系图
perf top -e cycles         #指定性能事件
perf top -p 23015,32476         #查看这两个进程的cpu cycles使用情况
perf top -s comm,pid,symbol         #显示调用symbol的进程名和进程号
perf top --comms nginx,top         #仅显示属于指定进程的符号
perf top --symbols kfree         #仅显示指定的符号
perf stat -r 10 ls > /dev/null         #执行10次程序，给出标准偏差与期望的比值
perf stat -v ls > /dev/null         #显示更详细的信息
perf stat -n ls > /dev/null         #只显示任务执行时间，不显示性能计数器
perf stat -e syscalls:sys_enter ls          #ls命令执行了多少次系统调用
perf record -e syscalls:sys_enter ls      #记录执行ls时的系统调用，可以知道哪些 系统调用最频繁
perf record -p `pgrep -d ',' nginx`      #记录nginx进程的性能数据
perf record ls -g    #记录执行ls时的性能数据
```
##### 选项说明
```
   annotate        Read perf.data (created by perf record) and display annotated code
   archive         Create archive with object files with build-ids found in perf.data file
   bench           General framework for benchmark suites
   buildid-cache   Manage build-id cache.
   buildid-list    List the buildids in a perf.data file
   c2c             Shared Data C2C/HITM Analyzer.
   config          Get and set variables in a configuration file.
   data            Data file related processing
   diff            Read perf.data files and display the differential profile
   evlist          List the event names in a perf.data file
   ftrace          simple wrapper for kernel's ftrace functionality
   inject          Filter to augment the events stream with additional information
   kallsyms        Searches running kernel for symbols
   kmem            Tool to trace/measure kernel memory properties
   kvm             Tool to trace/measure kvm guest os
   list            List all symbolic event types
   lock            Analyze lock events
   mem             Profile memory accesses
   record          Run a command and record its profile into perf.data
   report          Read perf.data (created by perf record) and display the profile
   sched           Tool to trace/measure scheduler properties (latencies)
   script          Read perf.data (created by perf record) and display trace output
   stat            Run a command and gather performance counter statistics
   test            Runs sanity tests.
   timechart       Tool to visualize total system behavior during a workload
   top             System profiling tool.
   probe           Define new dynamic tracepoints
   trace           strace inspired tool
```

---
