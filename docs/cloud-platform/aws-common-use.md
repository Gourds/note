### aws常用
#### 各区域ec2网络互连状况
> [https://www.cloudping.co/](https://www.cloudping.co/)

### EC2相关
#### aws磁盘扩容
> [https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html)

```bash
#https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html
#1.ext3/ext4
lsblk
growpart /dev/xvda 1
resize2fs /dev/xvda1
#2.xfs
lsblk
sudo yum install xfsprogs
growpart /dev/xvda 1
xfs_growfs -d /
```


