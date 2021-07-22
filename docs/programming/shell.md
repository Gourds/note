### 常用语法
#### shell数组
```bash
#A="a b c def" 這樣的變量只是將 $A 替換為一個單一的字串，但是改為 A=(a b c def) ，則是將 $A 定義為組數
${A[@]} 或 ${A[*]}
#可得到 a b c def (全部組數)
${A[0]}
#可得到 a (第一個組數)，${A[1]} 則為第二個組數...
${#A[@]} 或 ${#A[*]}  
#可得到 4 (全部組數數量)
${#A[0]}
#可得到 1 (即第一個組數(a)的長度)，${#A[3]} 可得到 3 (第四個組數(def)的長度)
A[3]=xyz  
#則是將第四個組數重新定義為 xyz
```
#### 变量替换
```bash
#file=/dir1/dir2/dir3/my.file.txt
${file#*/}：拿掉第一條 / 及其左邊的字串：dir1/dir2/dir3/my.file.txt
${file##*/}：拿掉最後一條 / 及其左邊的字串：my.file.txt
${file%/*}：拿掉最後條 / 及其右邊的字串：/dir1/dir2/dir3
${file:0:5}：提取最左邊的 5 個字節：/dir1
${file:5:5}：提取第 5 個字節右邊的連續 5 個字節：/dir2
${file/dir/path}：將第一個 dir 提換為 path：/path1/dir2/dir3/my.file.txt
${file//dir/path}：將全部 dir 提換為 path：/path1/path2/path3/my.file.txt
${#file} 可得到 27 ，因為 /dir1/dir2/dir3/my.file.txt 剛好是 27 個字節
```
#### 算术运算
```bash
#1. 使用expr，需注意空格,脚本中部分操作符需要使用"\"转义
A=`expr 1 + 5`
#2. 使用方括号"[]",空格无要求
A=$[1+2]
#3. 使用bc
echo "(5+6-1)/2" |bc
#4. 使用let
let A=1+2
#5. 使用declare
declare -i number2=2*3+5*13-32+25 && echo $number2
```
#### 条件测试
```bash
#1. 使用test
if test 1 -eq 1;then
#2. 使用方括号"[]",需在条件两边加上括号
if [ 1 -eq 1 ];then
```
#### 文件测试
```bash
-b filename	当filename 存在并且是块文件时返回真(返回0)
-c filename	当filename 存在并且是字符文件时返回真
-d pathname	当pathname 存在并且是一个目录时返回真
-e pathname	当由pathname 指定的文件或目录存在时返回真
-f filename	当filename 存在并且是正规文件时返回真
-g pathname	当由pathname 指定的文件或目录存在并且设置了SGID 位时返回真
-h filename	当filename 存在并且是符号链接文件时返回真 (或 -L filename)
-k pathname	当由pathname 指定的文件或目录存在并且设置了"粘滞"位时返回真
-p filename	当filename 存在并且是命名管道时返回真
-r pathname	当由pathname 指定的文件或目录存在并且可读时返回真
-s filename	当filename 存在并且文件大小大于0 时返回真
-S filename	当filename 存在并且是socket 时返回真
-t fd	当fd 是与终端设备相关联的文件描述符时返回真
-u pathname	当由pathname 指定的文件或目录存在并且设置了SUID 位时返回真
-w pathname	当由pathname 指定的文件或目录存在并且可写时返回真
-x pathname	当由pathname 指定的文件或目录存在并且可执行时返回真
-O pathname	当由pathname 存在并且被当前进程的有效用户id 的用户拥有时返回真(字母O 大写)
-G pathname	当由pathname 存在并且属于当前进程的有效用户id 的用户的用户组时返回真
file1 -nt file2	file1 比file2 新时返回真
file1 -ot file2	file1 比file2 旧时返回真
f1 -ef f2	files f1 and f2 are hard links to the same file
```
#### 数字测试
```bash
int1 -eq int2	如果int1 等于int2，则返回真
int1 -ne int2	如果int1 不等于int2，则返回真
int1 -lt int2	如果int1 小于int2，则返回真
int1 -le int2	如果int1 小于等于int2，则返回真
int1 -gt int2	如果int1 大于int2，则返回真
int1 -ge int2	如果int1 大于等于int2，则返回真
```
#### 字符串比较测试
```bash
-z string	字符串string 为空串(长度为0)时返回真
-n string	字符串string 为非空串时返回真
str1 = str2	字符串str1 和字符串str2 相等时返回真
str1 == str2	同 =
str1 != str2	字符串str1 和字符串str2 不相等时返回真
str1 < str2	按字典顺序排序，字符串str1 在字符串str2 之前
str1 > str2	按字典顺序排序，字符串str1 在字符串str2 之后
```
#### 判断字符串是否包含
```bash
#A包含B
[[ $strA =~ $strB ]]
[[ $A == *$B* ]]
```

### 一些例子
#### 多条件查找并删除
```bash
#查找删除5天以前的文件
find /data/bilogs_S3_haiwai/userinfo_guildinfo_zip/  -mtime +5 -name "*.*" |xargs  rm -Rf {};
```
#### 格式化输出printf
> [http://man.linuxde.net/printf](http://man.linuxde.net/printf)

```bash
echo -e "Now is printing server's information\n..."
printf "%-10s %-20s %-20s %-20s %-10s %-10s %-10s %-10s\n" OS TIME IP-in IP-out CPU-num MEM-all MEM-free NET-status > ${server_info_file}
for each_host in ${IP_list};do
    #s_addr_eth0=`ssh root@${each_host} "ifconfig eth0 | awk '/inet addr:/{ print $2 }' |  awk -F: '{print $2 }'"`
    s_os_release=`ssh root@${each_host} "cat /etc/redhat-release"|sed 's/.*release/release/g' |awk '{print $2}'`
    s_addr_eth0=`ssh root@${each_host} "ip addr | grep eth0 | grep inet"|awk '{print $2}' |awk -F/ '{print $1}'`
    s_addr_pub=`ssh root@${each_host} "curl -s http://1212.ip138.com/ic.asp" | egrep -o "(([0-9]{1,3}\.){3}[0-9]{1,3})"`
    s_cpu_core=`ssh root@${each_host} "cat /proc/cpuinfo |grep processor"|wc -l`
    s_mem_total=`ssh root@${each_host} "free -g | grep Mem" |awk '{print \$2}'`
    s_mem_use=`ssh root@${each_host} "free -g | grep Mem" |awk '{print $4}'`
    if [ `ssh root@${each_host} "ping -c5 -q www.baidu.com 2>&1" |grep received |awk -F, '{print $2}' |awk '{print $1}'` -ge 3 ] >/dev/null 2>&1;then net_status='yes' ;else net_status='no' ;fi
    s_net_status=$net_status
    s_date_time=`ssh root@${each_host} "date +%x-%T"`
    printf "%-10s %-20s %-20s %-20s %-10s %-10s %-10s %-10s\n" ${s_os_release:-Null} ${s_date_time:-Null} ${s_addr_eth0:-Null} ${s_addr_pub:-Null} ${s_cpu_core:-Null} ${s_mem_total:-Null} ${s_mem_use:-Null} ${s_net_status:-Null} >> ${server_info_file}
done
echo "Now is Done,The result file path is ${server_info_file}"
```
