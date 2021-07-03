### crontab服务
#### 基础说明
> crontab文件的格式：f1 f2 f3 f4 f5 program
> 其中f1-f5分别对应：分钟M、小时H、日D、月m、周d
> 例如：*  *  *  *  *  top
> - 第1列表示分钟1～59 每分钟用*或者 */1表示
> - 第2列表示小时1～23（0表示0点）
> - 第3列表示日期1～31
> - 第4列表示月份1～12
> - 第5列标识号星期0～6（0表示星期天）
> - 第6列要运行的命令或脚本
> 
**说明:** 
> - 当f1为*时表示每分钟都要执行program，f2为*时表示每小时都要执行程序，其馀类推
> - 当f1为a-b时表示从第a分钟到第b分钟这段时间内要执行, f2为a-b时表示从第a到第b小时都要执行，其馀类推
> - 当f1为*/n时表示每n分钟个时间间隔执行一次
> - 当f1为a, b, c,... 时表示第a, b, c,... 分钟要执行
> 
**Tips：** 
> - cmd要运行的程序，程序被送入sh执行，这个shell只有USER,HOME,SHELL这三个环境变量
> - 当程序在你所指定的时间执行后，系统会寄一封信给你，显示该程序执行的内容，若是你不希望收到这样的信，请在每一行空一格之后加上 `> /dev/null 2>&1` 即可

### 常用例子
#### 每晚的21:30重启apache
```bash
30 21 * * * /usr/local/etc/rc.d/lighttpd restart
```
#### 每月1、10、22日的4:45重启apache
```bash
45 4 1,10,22 * * /usr/local/etc/rc.d/lighttpd restart
```
#### 每周六周日的1:10重启apache
```bash
10 1 * * 6,0 /usr/local/etc/rc.d/lighttpd restart
```
#### 每天18:00至23:00之间每隔30分钟重启apache
```bash
0,30 18-23 * * * /usr/local/etc/rc.d/lighttpd restart
```
#### 每星期六的23:00重启apache
```bash
0 23 * * 6 /usr/local/etc/rc.d/lighttpd restart
```
#### 每一小时重启apache
```bash
* */1 * * * /usr/local/etc/rc.d/lighttpd restart
```
#### 晚上11点到早上7点之间，每隔一小时重启apache
```bash
* 23-7/1 * * * /usr/local/etc/rc.d/lighttpd restart
```
#### 每月的4号与每周一到周三的11点重启apache
```bash
0 11 4 * mon-wed /usr/local/etc/rc.d/lighttpd restart
```
#### 一月一号的4点重启apache
```bash
0 4 1 jan * /usr/local/etc/rc.d/lighttpd restart
```


### 常用命令行参数
```bash
crontab -e
#执行文字编辑器来设定时程表，内定的文字编辑器是 VI，如果你想用别的文字编辑器，则请先设定 VISUAL 环境变数来指定使用那个文字编辑器(比如说 setenv VISUAL joe)
crontab -r
#删除目前的时程表
crontab -l
#列出目前的时程表
crontab file [-u user]
#用指定的文件替代目前的crontab。
```


