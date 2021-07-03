### 常用简单示例
#### 指定行处理
```bash
#指定行处理（处理行号为3-11之间的行，并输出第一列，添加一列）
awk 'NR > 3 && NR <=11 {print $1,"new-field"}' xxx
```
#### 时间数学运算
```bash
awk 'BEGIN{FS=","} 
	{a="2019 01 01 " gensub(":"," ","g",$5);
	b="2019 01 01 " gensub(":"," ","g",$6);
	c = int((mktime(b)-mktime(a))/60)}
	{if (c >= 60){system("echo run command")}}' haha
```

---

### awk场景示例-1
| NumID | Name | Math | English | Chinese |
| --- | --- | --- | --- | --- |
| M5 | Arvon | 13 | 14 | 15 |
| F3 | Mo | 92 | 02 | 26 |
| F4 | Pikachu | 52 | 10 | 11 |
| M1 | Steavn | 1 | 2 | 3 |
| F2 | World | 4 | 5 | 56 |

#### 获取指定列
```bash
#获取姓名和英语成绩
awk -F' ' '{print $2,$4}' xxx.txt
```
#### 设置输入和输出分隔符
```bash
#将默认分割符空格设置为---
awk 'BEGIN{FS=" ";OFS="---"}{print $2,$4}' xxx.txt
```
#### 输出加描述字段
```bash
#输出行号列数带描述的姓名和成绩
awk 'BEGIN{FS=" ";OFS="---"}{print "filename:" FILENAME, "lineNum:"NR, "leishu:"NF, $2,$4}' xxx.txt
```
#### 添加Title和结束符并设置输入输出分隔符
```bash
awk 'BEGIN{print "T1","T2","T3","T4"}{FS=" ";OFS="---"}{print "filename:" FILENAME, "lineNum:"NR, "leishu:"NF, $2,$4}END{print "Game Over"}' xxx.txt
```
#### 匹配指定内容输出
```bash
 #匹配Arvon并输出成绩
 awk 'BEGIN{FS=" "}/Arvon/{print $0}' xxx.txt
 awk 'BEGIN{FS=" "}/M[1-9]/{print $0}' xxx.txt
 awk 'BEGIN{FS=" "}/M./{print $0}' xxx.txt
```
#### 高阶：列累加
```bash
#获取所有人每科成绩的总和
awk 'BEGIN{FS=" "}/F[1-9]|M[1-9]/{sMath=sMath+$3;sEnglish=sEnglish+$4;sChinese=sChinese+$5}END{print sMath,sEnglish,sChinese}' xxx.txt
```
#### 高阶：筛选计数
```bash
#获取数学成绩大于10的人数，并列出是谁
awk 'BEGIN{renshu=0}{FS=" "}/F[1-9]|M[1-9]/{if ($3>10) {print $0; renshu+=1}}END{print "totleNum:" renshu}' xxx.txt
```
#### 高阶：过滤筛选求和
```bash
#求英语成绩大于等于5的人的各科成绩总和
awk 'BEGIN{sMath=0;sEng=0;sChi=0}/F[1-9]|M[1-9]/{if($4>=5){print $0;sMath+=$3;sEng+=$4;sChi+=$5}}END{print "sMath:" sMath, "sEng:" sEng, "sChi:" sChi}' xxx.txt
```
### 基础说明
> **名词解释** 
> - 内建变量
> - Record（记录）：awk从数据文件上读取数据的基本单位，默认内建变量RS为换行
>    -  如：例子中的“M5	Arvon	13	14	15”就是一条记录
> - Field(字段）：记录中被分隔开的子字符串，默认内建变量FS为空格
>    -  如：例子中第一条记录的第一个字符串为M5，第二个为Arvon


---

