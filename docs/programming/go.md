

### 基础

按数据类型分
  - 基础数据类型：整数、浮点数、字符串、数组
  - 复合数据类型：切片、map、chan
- 值类型：变量存储的是具体的值
- 引用类型：变量存储的是存储具体值的内存地址

注意点
- 基础类型基本都是值类型
- 复合数据类型一般都是引用类型

defer：延迟函数调用，只是延迟调用，函数如果有参数，参数是已经固定了的
- 延迟调用是栈结构，多个延迟调用遵循先进后出
- 延迟调用在上级函数结束前或return返回前执行
- 延迟函数可以用来关闭文件、或在程序崩溃前执行一些操作，因为会在panic传递给调用函数前执行

return：返回，可以为空，可以有多个值
- 函数定义返回可以直接命名返回值，在函数体中就不需要重新声明了，还可以直接return返回
- return可以用来结束循环体

```go
func f1()(s1 int, s2 int) {
	return
}
//以上函数调用，会返回两个0值，因为s1和s2的默认值就是0
```

#### 格式化输出

|表示|描述|
|---|---|
|%v|值的默认格式|
|%T|类型|
|%t|布尔值|
|%p|指针，十六进制表示|
|%%|输出%字符本身|
|%s|字符串|
|%d|十进制|
|%f|浮点数|
|%b|二进制|
|%x|十六进制，字母a-f|
|%X|十六进制，字母A-F|
|%o|八进制|
|%c|相应Unicode码点所表示的字符|

### 安装配置

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

##### go mod
新版本1.11开始支持。需启用`GO111MODULE=on`,默认为auto，auto会兼容之前的vender及go path的模式，直接删除GOPATH就行了，使用`GO11MODULE`项目最好在GOPATH路径之外

```bash
#确认 GO111MODULE=on  or auto
#pwd github.com/gourds/upload2remote
cd github.com/gourds/upload2remote
go mod init github.com/gourds/upload2remote
go mod tidy
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

```bash
#Mac
go get -v -u golang.org/x/tools/cmd/godoc
godoc -http=:9090
#
```
