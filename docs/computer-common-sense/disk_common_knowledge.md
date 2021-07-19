### 基本概念

- IOPS：Input/Output Per Second， 即每秒系统能能处理的I/O请求数量
- TPUT: 吞吐量

>**Tips:**
1. 随机读写频繁的应用，如小文件存储(图片)、数据库，关注随机读写性能，IOPS是关键衡量指标。
2. 顺序读写频繁的应用，如电视台的视频编辑，备份系统，关注连续读写性能。数据吞吐量是关键衡量指标。

#### 硬盘接口

#### 机械硬盘
机械硬盘除以上指标还有两个物理指标：`寻道时间`和`旋转延迟`
- 7200 转/分的STAT硬盘平均物理寻道时间是9ms，平均旋转延迟大约 4.17ms
- 10000转/分的STAT硬盘平均物理寻道时间是6ms ，平均旋转延迟大约 3ms
- 15000转/分的SAS硬盘平均物理寻道时间是4ms，平均旋转延迟大约 2ms

最大IOPS的理论计算方法 ： IOPS = 1000 ms/ (寻道时间 + 旋转延迟)。忽略数据传输时间。

- 7200 rpm的磁盘IOPS = 1000 / (9 + 4.17) = 76 IOPS
- 10000 rpm的磁盘IOPS = 1000 / (6+ 3) = 111 IOPS
- 15000 rpm的磁盘IOPS = 1000 / (4 + 2) = 166 IOPS

RAID0/RAID5/RAID6的多块磁盘可以并行工作，所以，在这样的硬件条件下， IOPS 相应倍增。假如， 两块15000 rpm 磁盘的话，166 * 2 = 332 IOPS。

#### 固态硬盘

闪存颗粒一般按P/E寿命排列是SLC>MLC>TLC>QLC

|Type|描述|备注|
|:---:|:---:|:---:|
|SLC||
|MLC||
|TLC||
|QLC||

接口类别

|接口|一般速度|描述|
|---|---|---|
|IDE（PATA接口）|并行接口 传输速率只能达到133MB/s||
|SATA|SATA是Serial ATA的缩写，即串行ATA，是一种电脑总线，主要用于主板和大容量存储设备，比如硬盘或光驱等设备之间的数据传输. <ul><li>Serial ATA1.5Gbps（1.0+标准）的传输率已经达到150MB/s</li><li>Serial ATA 3Gbps（2.0+标准）就达到了300MB/s</li><li>Serial ATA6Gbps（3.0标准）更是达到了600MB/s</li></ul>|目前主流的消费级sata固态硬盘的普遍读写速度在480MB/s~540MB/s这个区间|
|mSATA|mSATA SATA协会开发的新的mini-SATA(mSATA)接口控制器的产品规范，新的控制器可以让SATA技术整合在小尺寸的装置上。虽然mSATA是sata接口，但使用mini PCIe界面进行传输信号<ul><li>一般来说，1.8寸SATA的，即MicroSATA，有SSD也有HDD的。</li><li>比1.8寸更小的miniPCIE卡大小的SATA才是mSATA，目前只有SSD</li><li>mSATA接口，其实就是SATA接口的mini版，所以传输速率和SATA接口一致，有1.5Gbps、3Gbps、6Gbps三种模式</li></ul>||
|U.2|别称SFF-8639，是由固态硬盘形态工作组织（SSD Form Factor Work Group）推出的接口规范。U.2不但能支持SATA-Express规范，还能兼容SAS、SATA等规范其理论带宽高达32Gbps与M.2接口无异|价格较为昂贵，只能算是一个较为冷门的分支|
|SCSI||
|SAS||
|光纤通道||

传输协议

|协议|描述|
|---|---|
|Nvme|Nvme协议走的是PCI总线，根据PCIe版本还会有不同的速度，目前最快是PCIe 4.0×4的规格，达7.88 GB/s，常见的PCIe 3.0×4是3.94 GB/s|
|sata||
