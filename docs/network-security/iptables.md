#### 场景规则
```bash
#对本机lo回环地址放开
iptables -I INPUT -i lo -j ACCEPT
#对本机访问外部网络放开(ESTABLISHED表示tcp的一种状态，RELATED为ftp的一种状态)
iptabels -I INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
#对所有地址开放本机tcp（80，22，10-21）端口
iptabels -I INPUT -p tcp --dport 22 -j ACCEPT 
iptabels -I INPUT -p tcp --dports 10:21 -j ACCEPT -m comment --comment " 2015-09-24 by arvon"
#允许所有的地址开放本机的基于ICMP协议的数据包访问
iptables -I INPUT -p icmp -j ACCEPT
#ftp主动模式下规则
iptables -I INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -I INPUT -p tcp --dport 21 -j ACCEPT
#ftp被动模式下规则
iptables -I INPUT -p tcp --dport 21 -j ACCEPT
  ##vim /etc/vsftpd/vsftpd.conf
  #pasv_min_port=50000
  #pasv_max_port=60000
iptables -I INPUT -p tcp --dports 50000:60000 -j ACCEPT
#ftp被动模式下规则二
iptables —I INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -I INPUT -p tcp --dport 21 -j ACCEPT
  #modprobe nf_conntrack_ftp #临时开启内核连接追踪模块ftp
  #vim /etc/sysconfig/iptables-config	#开机自动加载
  ##IPTABLES_MODULES="nf_conntrack_ftp"
#防CC攻击
#对源地址为12并且并发大于10的数据包进行拒绝并返回错误信息
iptables -I INPUT -p tcp --dport 80 -s 192.168.1.12 -m connlimit --connlimit-above 10 -j REJECT
#当icmp不超过10个时放行，当超过10个每分钟放行1个
iptables -A INPUT -p icmp -m limit --limit 1/m --limit-burst 10 -j ACCEPT
iptables -A INPUT -p icmp DROP
#防SYN攻击
iptables -N syn-flood
iptables -A INPUT -p tcp --syn -j syn-flood
iptables -I syn-flood -p tcp -m limit --limit 3/s --limit-burst 6 -j RETURN
iptables -A syn-flood -j REJECT
#转发
iptables -A FORWARD -p tcp -s 10.10.0.0/24 -m multiport --dports 80,110,21 -j ACCEPT
#工作日工作时间禁止访问tencent的域名
iptables -I FORWARD -p udp --dport 53 -m string --string "tencent" -m time --timestart 8:00 --timestop 12:00 --days Mon,Tue,Wed,Thu,Fri,Sat -j DROP
#内核参数调整
sysctl -w net.ipv4.ip_forward=1 #开启内核数据包转发功能
sysctl -w net.ipv4.tcp_syncookies=1 #开启cookies验证，一定程度防止syn攻击
```
#### iptables命令
```bash
#列出所有规则
iptables -nVL
#添加规则
iptables -I INPUT -s 192.168.1.0/24 -p tcp -m multiport --dport 22,80 -j ACCEPT
#删除一条规则
iptables -D INPUT -p icmp -j ACCEPT
iptables -D INPUT 2
#清除所有规则
iptables -F 
#SNAT
iptables -t nat -A POSTROUTING -s 10.10.10.177.0/24 -j SNAT --to 10.10.188.111
#DNAT
iptables -t nat -A PREROUTING -d 10.10.188.111 -p tcp --dport 80 -j DNAT --to 10.10.177.222:80
#在最后添加默认拒绝的规则
iptables -P INPUT DROP
```
#### iptables基础
> #### Iptables组成规则：四表+五链(hook point)+规则
> **四表** 
> - filter表：访问控制，规则匹配
> - nat表：请求转发
> - mangle表：修改数据包，改变包头中内容（TTL、TOS、MARK），需要对应交换机支持
> - raw表
> 
**五条链** 
> - INPUT
> - OUTPUT
> - FORWARD
> - PREROUTING
> - POSTROUTING
> 
**数据包访问控制** 
> - ACCEPT：接收数据包
> - DROP：直接丢弃数据包，不给客户端返回信息
> - REJECT：丢弃数据包并给客户端返回信息
> 
**数据包改写** 
> - SNAT：对源地址进行改写
> - DNAT：对目标地址进行改写
> 
**信息记录** 
> - LOG：将访问信息记录到log

