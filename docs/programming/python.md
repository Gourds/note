### 常用方法


### Jupyter

```
python -m venv ~/Documents/jupyterlab/
source ~/Documents/jupyterlab/venv/bin/activate
pip install jupyterlab
cd workdir
jupyter lab
```

### venv

```bash
#默认
virtualenv --no-site-packages venv
#指定python
python3 -m venv /path/to/new/virtual/environment
#启用
source venv/bin/activate
#退出
deactivate
```

### 生成reque

仅生成项目中需要的包
```bash
pip install pipreqs
pipreqs .
```

会生成当前环境中所有的包包括未使用的
```bash
pip freeze > requirements.txt
```

#### list交集并集差集处理
```bash
交集
list(set(a).intersection(set(b)))
并集
list(set(a).union(set(b)))
差集
list(set(b).difference(set(a))) # b中有而a中没有的
```
#### 正则转义
```bash
 \d #匹配任何十进制数相当于[0-9]
 \D #匹配任何非数字字符，相当于[^0-9]
 \s #匹配任何空白字符，相当于[\t\n\r\f\v]
 \S #匹配任何非空白字符，相当于[^\t\n\r\f\v]
 \w #匹配任何字母数字字符，相当于[a-zA-Z0-9_]
 \W #匹配任何非字符数字字符，相当于[^a-zA-Z0-9_]
```
### 数据库操作
```bash
import MySQLdb
#coding:utf-8
#mysql>create table mytable (id int , username char(20));
conn = MySQLdb.connect(user='root',passwd='admin',host='127.0.0.1')
#连接到数据库服务器
cur = conn.cursor()
#连接到数据库后游标的定义
conn.select_db('test')
#连接到test数据库
cur.execute("insert into mytable(id,username) value(2,'mo');")
#插入一条数据
sqlim = "insert into mytable(id,username) values(%s,%s);"
cur.executemany(sqli,[(4,'haha'),(5,'papa'),(6,'dada')])
#使用格式化字符串，一次添加多条数据，同理可应用于修改和删除
cur.execute('delete from mytable where id=4')
#删除一条数据
cur.execute("update mytable set username='gogo' where id=5")
#修改一条数据
cur.execute("select * from mytable")
cur.fetchone()
cur.scroll(0,'absolute')
cur.fetchmany()
#查询一条数据，先select出数据条目数量，再通过fetchone依次取值,取值完成后可以通>>过scroll重新定义游标位置，如上为让游标在到开头，使用getchmany可以以元组形式取出
所有值
cur.fetchmany(cur.execute("select* from mytable"))
#使用这种方法可以直接取出所有值
cur.close()
#关闭游标
conn.close()
#关闭数据库连接
```
### 安装及配置相关
```bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simpl
pip install boto3==1.6.14  botocore==1.9.14  -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com
```
#### 源码安装
```bash
yum -y install gcc* make* cmake openssl* autoconf libtool libevent-devel pcre pcre-devel zlib
wget https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tgz
tar -zxvf Python-3.6.8.tgz
cd Python-3.6.8
./configure
make
make install
python3 -m venv ./py_venv
cd py_venv
source bin/activate
```

```bash
wget https://www.python.org/ftp/python/3.9.0/Python-3.9.0.tgz -O /tmp/Python-3.9.0.tgz
cd /tmp && tar zxf Python-3.9.0.tgz
cd Python-3.9.0
yum groupinstall -y 'Development Tools'
yum install -y gcc openssl-devel bzip2-devel libffi-devel
./configure prefix=/usr/local/python3
make && make install
export PATH=$PATH:/usr/local/python3/bin/
#因为在旧版本的 gcc 中使用 --enable-optimizations 可能会有问题，升级gcc后可以替换成：
#./configure prefix=/usr/local/python3 --enable-optimizations
```

#### 导出依赖
```bash
virtualenv env1 --no-site-packages
#导出requirements
pip freeze > requirements.txt
pip install -r requirements.txt
pip install pipreqs
pipreqs ./ --encoding=utf8
#Logging 模块 https://www.cnblogs.com/CJOKER/p/8295272.html
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple git
```
### 学习文章
> - [一篇文章搞懂Python中的面向对象编程](http://yangcongchufang.com/%E9%AB%98%E7%BA%A7python%E7%BC%96%E7%A8%8B%E5%9F%BA%E7%A1%80/python-object-class.html) 
