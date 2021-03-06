**概述：** ICMP（互联网控制消息协议）介绍
> - ICMP（Internet Control Message Protocol）是介于网络层和传输层的协议，主要功能为传输网络诊断信息。
> - ICMP传输的信息可以分为两类，一类是错误(error)信息，这一类信息可用来诊断网络故障。我们已经知道，IP协议的工作方式是“Best Effort”，如果IP包没有被传送到目的地，或者IP包发生错误，IP协议本身不会做进一步的努力。但上游发送IP包的主机和接力的路由器并不知道下游发生了错误和故障，它们可能继续发送IP包。通过ICMP包，下游的路由器和主机可以将错误信息汇报给上游，从而让上游的路由器和主机进行调整。需要注意的是，ICMP只提供特定类型的错误汇报，它不能帮助IP协议成为“可靠”(reliable)的协议。

> - 另一类信息是咨询(Informational)性质的，比如某台计算机询问路径上的每个路由器都是谁，然后各个路由器同样用ICMP包回答。

> - ICMP基于IP协议。也就是说，一个ICMP包需要封装在IP包中，然后在互联网传送。ICMP是IP套装的必须部分，也就是说，任何一个支持IP协议的计算机，都要同时实现ICMP。

> - ICMP包都会有Type, Code和Checksum三部分。Type表示ICMP包的大的类型，而Code是一个Type之内细分的小类型。针对不同的错误信息或者咨询信息，会有不同的Type和Code。从上面我们可以看到，ICMP支持的类型非常多，就好像瑞士军刀一样，有各种各样的功能。Checksum与IP协议的header checksum相类似，但与IP协议中checksum只校验头部不同，这里的Checksum所校验的是整个ICMP包(包括头部和数据)。余下的ICMP包格式根据不同的类型不同。另一方面，ICMP包通常是由某个IP包触发的。这个触发IP包的头部和一部份数据会被包含在ICMP包的数据部分。

> - ICMP协议是实现ping命令和traceroute命令的基础。这两个工具常用于网络排错

