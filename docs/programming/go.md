

##### 编译

>交叉编译
#GOOS：目标平台的操作系统（darwin、freebsd、linux、windows）
#GOARCH：目标平台的体系架构（386、amd64、arm）
#交叉编译不支持 CGO 所以要禁用它

```
go build mail.go
#单个
go build  -o ./upload2remote
#可以传变量
go build -ldflags "-X cmd.INFO.Version=2.0.0 -X cmd.INFO.Auther=gourds" upload2remote/
```

```
#Mac
# linux 下去执行
CGO_ENABLED=0  GOOS=linux  GOARCH=amd64  go build main.go
# Windows 下去执行
CGO_ENABLED=0 GOOS=windows  GOARCH=amd64  go  build  main.go

#Linux下编译
# Mac  下去执行
CGO_ENABLED=0 GOOS=darwin  GOARCH=amd64  go build main.go
# Windows 下执行
CGO_ENABLED=0 GOOS=windows  GOARCH=amd64  go build main.go

#Windos下编译
# Mac 下执行
SET  CGO_ENABLED=0
SET GOOS=darwin
SET GOARCH=amd64
go build main.go
# Linux 去执行
SET CGO_ENABLED=0
SET GOOS=linux
SET GOARCH=amd64
go build main.go
```

##### 安装



goland
```
https://www.jetbrains.com/go/download/download-thanks.html?platform=mac
```
配置永久试用`https://zhile.io/`


```bash
https://golang.org/dl/

国内下载：https://golang.google.cn/dl/go1.11.2.linux-amd64.tar.gz
wget https://golang.org/dl/go1.11.2.linux-amd64.tar.gz
tar -C /usr/local -xzf go1.11.2.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
go version
```
