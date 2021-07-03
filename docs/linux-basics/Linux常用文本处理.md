### grep命令
##### grep常用参数
```
    -e: 使用正则搜索
    -i: 不区分大小写
    -v: 查找不包含指定内容的行
    -w: 按单词搜索
    -c: 统计匹配到的次数
    -n: 显示行号
    -r: 逐层遍历目录查找
    -A: 显示匹配行及前面多少行, 如: -A3, 则表示显示匹配行及前3行
    -B: 显示匹配行及后面多少行, 如: -B3, 则表示显示匹配行及后3行
    -C: 显示匹配行前后多少行,   如: -C3, 则表示显示批量行前后3行
    --color: 匹配到的内容高亮显示
    --include: 指定匹配的文件类型
    --exclude: 过滤不需要匹配的文件类型
```

---

### sed命令
##### Ex1：注释某行
```bash
sed -i 's/.*book.*/#&/g' file
```
##### Ex2：查找替换
```bash
find ./config/ -type f |xargs grep "hello.com" |awk -F: '{print $1}' |xargs sed -i 's@hello.com@world.com@g'
```
##### Ex3: 取消文本换行
```bash
sed ":a;N;s/\n//g;ta" a.txt
sed ':label;N;s/\n/:/;b label' filename
sed ':label;N;s/\n/:/;t label' filename
```
##### Ex4: 删除匹配行及其后两行
```xml
find ./ -type f |xargs sed -i '' '/conditions/,+2d'
```
##### Ex5：每5行输出加空行
```xml
sed 'N;N;N;N;/^$/d;G' file.txt
#每行后面加空格
sed  G  tmp
sed  'G;G'  tmp #每行后面加两行空格
#每行前面加空格
sed  '{x;p;x;}' tmp
#匹配行后加空格
sed '/shui/G' tmp  
#匹配行前加空格
sed '/shui/{x;p;x;}' tmp
#每两行前加空格
sed 'N;/^$/d;{x;p;x;}' tmp
```







---

### 
