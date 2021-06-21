# Python
推荐使用 PyCharm 这个IDE进行代码编写。
Java 是强类型，编译型语言。
[Python](https://www.python.org/) 是弱类型、解释型语言，运行速度比java慢。python的源码不能加密。

```text
python -V  //查看当前版本   python版本
import sys
print (sys.version)
```

##  python3 数据类型
Python中的变量不需要声明。
```text
1、字符串
2、数字
3、列表
4、元组
5、集合
6、字典
7、日期
```

## if 判断
```text
 age = 20
 if age > 18:
     print('aaa')
 elif age >= 6:
     print('bbb')
 else:
     print('ccc')
```

## for 循环
```text
list = [100, 200, 300]
for temp in list:
   print(temp)
```


## while 循环
```text
N = 10
x = 0
while x < N:
   x = x + 1
   print(x)
   if x > 6:
       break
```



## 列表
```text
# 集合中数据类型可以不一样的
mList = ['Michael', 100, True]
print("第一个数据=" + mList[0])
print("最后一个数据=" + str(mList[-1]))
print("数组大小=" + str(len(mList)))
mList.append('AA')
mList.insert(0, 'BB')
# 删除最后一个
mList.pop()
# 删除指定索引的数据
mList.pop(1)
```


##  元组
类似java中的数组
```text
t = ('AA', 1 , True)
print (t)
```


## 字典
```text
dict = {
   'Adam': 95,
   'Lisa': "aaaaaaa",
   'Bart': True
}

# 根据 key 获得 值
for mkey in dict:
   print(dict[mkey])

# 获得全部的值
for temp in dict.values():
   print(temp)

# 取出键值对
for key, value in dict.items():
   print(key, ' ，', value)
```

## 类、对象
```text
# 继承自 object
class Person3(object):
   # 类属性
   address='地球'

   # 构造方法
   def __init__(self, name):
       # 对象属性
       # 公开
       self.name = name
       # 公开
       self._sex = '女'
       #私有
       self.__job = '学生'#外部不可以访问

   # 类方法
   @classmethod
   def getAddress(self):
       return self.address

   # 对象方法
   def printInfo(self):
       print("当前对象名字是：", self.name)


# 设置、获取属性
p= Person3('小芳')
setattr(p, 'name', 'small dog')
msg = getattr(p, 'name')

# 对象类型判断
if isinstance(p,object):
   print('类型判断正确')
else:
   print('类型判断错误')
```

## 连接 mysql
安装 pymysql 插件。 如果使用的是pycharm， 是可以指定python环境目录的。方便灵活切换2.x和3.x .
```text
import pymysql
from poetry.author import Author

conn= pymysql.connect( host='114.xx.xx.65', port=3306, user='xx', passwd='xx',db='xx',charset='UTF8')
print("获得数据库连接:")
print(conn)
cursor = conn.cursor()

def addAuthor(author):
    sql = ' insert  into poetryauthor( mname, msex , mdesc, mpic,mtime,mtype ) values(  "%s" ,"%s" ,"%s" ,"%s" ,"%s" ,"%s" ) '\
         % (author.mname,  author.msex, author.mdesc, author.mpic, author.mtime, author.mtype)
    cursor.execute( sql)
    newId = int(conn.insert_id())
    conn.commit()
    print("插入 新记录的id是:  ", newId)

def deleteAuthorById( mId):
    sql = 'delete from poetryauthor where mId = %d  '% (mId)
    count = cursor.execute(sql)
    conn.commit()
    print("删除结果：", count)

def updateAutorById(author ,mId):
    sql = 'update poetryauthor set mname="%s", msex="%s", mdesc="%s", mpic="%s", mtime="%s" , mtype="%s" where mId=%d ' \
          % (author.mname, author.msex, author.mdesc, author.mpic, author.mtime, author.mtype, mId)
    count = cursor.execute(sql)
    print("更新结果：", count)
    conn.commit()

def queryAuthor():
    count=cursor.execute('select * from poetryauthor')
    print('共有'+str(count)+' 条记录')
    rows = cursor.fetchall()
    for row in rows:
        mid   = row[0]
        mname = row[1]
        msex  = row[2]
        mdesc = row[3]
        mpic  = row[4]
        mtime = row[5]
        mtype = row[6]
        print(mid, mname, msex , mdesc, mpic,mtime,mtype)

# 测试
author = Author("李白2",  "男1" ,  "牛逼诗人1",  "baidu.com...png1",   "唐代1"  , "浪漫主义1")
# addAuthor(author)
# queryAuthor()
# deleteAuthorById(5)
# updateAutorById(author , 6)
# queryAuthor()

# 关闭连接等...
cursor.close()
conn.commit()
conn.close()
```

## 中文乱码
```text
1、先用chardet检测读取到的内容的编码
chardet.detect(content)
2、转换成合适的编码格式
html = unicode(content, "gb2312").encode("utf8")
```

## pip 安装库
pip 是Python的软件包管理系统，Python语言自带的命令行工具，它可以安装和管理第三方软件包。
```text
pip install requests
pip install beautifulsoup4
http://www.lfd.uci.edu/~gohlke/pythonlibs/
```


## urllib、urllib2
在Python3已拆分更名为urllib.request和urllib.error
```text
urllib  GET
f=urllib.urlopen("http://m.cnblogs.com/")
s=f.read()
print s

带参数 GET
params = urllib.urlencode({'id': 8, 'name': 'jack', 'age': 25})
f = urllib.urlopen("http://localhost:18797/MailClient/test.aspx?%s" % params)
print f.read()

POST
params = urllib.urlencode({'id': 8, 'name': 'jack', 'age': 25})
f = urllib.urlopen("http://localhost:18797/MailClient/test.aspx",params)
print f.read()

import  urllib
URL='http://192.168.0.102:8081/findYou/pointsApi/queryAllPointsTaskFy/{offset}/{limit}'
URL= URL.replace('{offset}','0')
URL = URL.replace('{limit}','1')
f=urllib.urlopen(URL)
s=f.read()
print s

//下载
import urllib
import urllib.request
import os
def downCallBack(a, b, c):
    '''''
    a:已经下载的数据块
    b:数据块的大小
    c:远程文件的大小
   '''
    per = 100.0 * a * b / c
    if per > 100:
        per = 100
    print('下载进度：  %.2f%%' % per)

url = 'http://www.python.org/ftp/python/2.7.5/Python-2.7.5.tar.bz2'
local = os.path.join('F:\\pythonDown\\', 'downDemo.zip')
urllib.request.urlretrieve(url, local, downCallBack)
```