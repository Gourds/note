**概述：** Rsync文件同步服务器个人理解传送大量资源文件或较多小文件时适合使用，具有数据验证、断点续传、增量传输、差异传输等特性。优于scp及ftp等工具。

### 使用
rsync 的最大特点是会检查发送方和接收方已有的文件，仅传输有变动的部分（默认规则是文件大小或修改时间有变动）
- rsync会同步以"点"开头的隐藏文件，如果要排除隐藏文件，可以这样写`--exclude=".*"`
- rsync 默认使用 SSH 进行远程登录和数据传输,早期rsync不使用SSH协议，需要用`-e ssh`参数指定协议，现在可以省略
- 如果远程服务器安装并运行了rsync守护程序，则可以用rsync://协议（默认端口873）进行传输，服务器与目标目录之间使用双冒号分隔`::`或`rsync://`

```bash
/bin/rsync -auvP --delete -e "ssh -p 10022" /source/dir/ root@dest.host:/dest/dir/ >> /tmp/rsync_rst.log 2>&1

#查看哪些文件会被同步，不会真正执行
rsync -anv /source/dir/ root@dest.host:/dest/dir/
#使用rsync协议
rsync -av source/ remote.host::/dest/dir/
rsync -av source/ rsync://remote.host/dest/dir/
```


|参数|描述|
|---|---|
|-a|archive mode; equals -rlptgoD|
|-r|递归|
|-l|copy symlinks as symlinks|
|-p|preserve permissions|
|-t|preserve modification times|
|-g|preserve group|
|-o|preserve owner (super-user only)|
|-D|same as --devices --specials|
|--delete|默认情况下，rsync 只确保源目录的所有内容（明确排除的文件除外）都复制到目标目录。它不会使两个目录保持相同，并且不会删除文件。如果要使得目标目录成为源目录的镜像副本，则必须使用--delete参数，这将删除只存在于目标目录、不存在于源目录的文件。|
|-n,--dry-run|模拟执行结果|
|--exclude|排除文件，支持通配符|
|--append|接着上次中断的地方，继续传输|
|--append-verify|跟--append参数类似，但会对传输完成后的文件进行一次校验。如果校验失败，将重新发送整个文件|
|--bwlimit|指定带宽限制，默认单位是 KB/s，比如--bwlimit=100|
|-c,--checksum|参数改变rsync的校验方式。默认情况下，rsync 只检查文件的大小和最后修改日期是否发生变化，如果发生变化，就重新传输；使用这个参数以后，则通过判断文件内容的校验和，决定是否重新传输|
|--progress|显示进展|


> 参阅
> - [我的博客-Rsync服务安装配置记录](https://arvon.top/2017/07/24/Rsync%E6%9C%8D%E5%8A%A1%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE%E8%AE%B0%E5%BD%95/) 
