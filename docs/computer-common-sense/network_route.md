
### Linux路由配置

使用命令
```bash
netstat -nr
route -n
#注意mac系统下的route命令与unix不同，不能这么用
```

输出
```
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.33.0.1       0.0.0.0         UG    0      0        0 eth0
10.33.0.0       0.0.0.0         255.255.255.0   U     0      0        0 eth0
169.254.169.254 0.0.0.0         255.255.255.255 UH    0      0        0 eth0
```
说明

|输出|说明|
|---|---|
|Destination|目标网段或者主机|
|Gateway|网关地址，"*"表示目标是本主机所属的网络，不需要路由|
|Genmask|掩码|
|Flags|标记。常见标记如：<br> U(活动的路由)<br> H(目标是一个主机) <br> G(目标是一个网关) <br> R(恢复动态路由缠身的表项) <br> D(由路由的后台程序动态的安装)<br> M(由路由的后台程序修改)|
|Metric|路由距离，到达指定网络所需的中转数（linux内核中未使用）|
|Ref|路由项引用次数（Linux内核中未使用）|
|Use|该路由被路由软件查询的次数|
|Iface|该路由对应的网络接口|

三种路由类型说明
- 主机路由：主机路由是路由选择表中指向单个IP地址或主机名的路由记录。主机路由的Flags字段为H。
- 网络路由：网络路由是代表主机可以到达的网络。网络路由的Flags字段为N。
- 默认路由：当主机不能在路由表中查找到目标主机的IP地址或网络路由时，数据包就被发送到默认路由（默认网关）上。

### 一般路由协议优先级

|协议|优先级|备注|
|---|---|---|
|DIRECT|0||
|OSPF|10||
|IS-IS Level 1|15||
|IS-IS Level 2|18||
|NSFnet主干的SPF|19||
|缺省网关和EGP缺省|20||
|重定向路由|30||
|由route socket得到的路由|40||
|由网关加入的路由|50||
|路由器发现的路由|55||
|静态路由|60||
|CISCO IGRP|80||
|DCN HELLO|90||
|Berkeley RIP|100||
|点对点接口聚集的路由|110||
|Down状态的接口路由|120||
|聚集的缺省路由|130||
|OSPF的扩展路由|140||
|BGP|170||
|EGP|200||

Tips：思科、华为路由器的路由优先级也有不同

- 路由优先级在不同协议时候，比较preference的大小，而在路由协议相同时候由于preference相同，则再比较metric的大小，进而确定最终选择的路由
- 度量值（Metric）指明了路径的优先权，而管理距离（AD）指明了发现路由方式的优先权。同一种路由协议比较度量值，而不同路由协议比较管理距离
- 同种协议管理距离一样 ，所以比较metric ，不同协议比较管理距离 越小越优先 。