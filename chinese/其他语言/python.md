# [python](https://www.python.org/)
推荐使用 PyCharm 这个IDE进行代码编写。

## python 缺点
 ```xml
 python的源码不能加密。
 python是解释型语言，运行速度比java慢。
 ```


##  python3 数据类型
Python中的变量不需要声明。
```python
1、字符串
2、数字
3、列表
4、元组
5、集合
6、字典
7、日期
```

### if 判断
```python
 age = 20
 if age > 18:
     print('aaa')
 elif age >= 6:
     print('bbb')
 else:
     print('ccc')
```

## for 循环
```python
list = [100, 200, 300]
for temp in list:
   print(temp)
```


## while 循环
```python
N = 10
x = 0
while x < N:
   x = x + 1
   print(x)
   if x > 6:
       break
```



## 列表
```python
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


###  元组
类似java中的数组
```python
t = ('AA', 1 , True)
print (t)
```


## 字典
```python
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
```xml
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
```xml
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


## 古诗文网 爬取作者信息 (静态页面)
### 数据库帮助类
```xml
class MySqlTool(object):
    def __init__(self):
        self.conn = pymysql.connect(host='xx.xx.xx.xx65', port=3306, user='xx', passwd='xx',
                                    db='xx', charset='UTF8')
        print("获得数据库连接:")
        print(self.conn)
        self.cursor = self.conn.cursor()

    def addAuthor(self ,author):
        sql = " insert  into poetryauthor( mname, msex , mdesc, mpic,mtime,mtype ) values(  '%s','%s','%s','%s','%s','%s'  ) " \
              % (author.mname, author.msex, author.mdesc, author.mpic, author.mtime, author.mtype)
        self.cursor.execute(sql)
        newId = int(self.conn.insert_id())
        self.conn.commit()
        print("插入 新记录的id是:  ", newId)

    def deleteAuthorById(self, mId):
        sql = 'delete from poetryauthor where mId = %d  ' % (mId)
        count = self.cursor.execute(sql)
        self.conn.commit()
        print("删除结果：", count)

    def updateAutorById(self, author, mId):
        sql = " update poetryauthor set mname= '%s', msex='%s', mdesc='%s', mpic='%s', mtime='%s' , mtype='%s' where mId=%d " \
              % (author.mname, author.msex, author.mdesc, author.mpic, author.mtime, author.mtype, mId)
        count = self.cursor.execute(sql)
        print("更新结果：", count)
        self.conn.commit()

    def queryAuthor(self):
        count = self.cursor.execute('select * from poetryauthor')
        print('共有' + str(count) + ' 条记录')
        rows = self.cursor.fetchall()
        for row in rows:
            mid = row[0]
            mname = row[1]
            msex = row[2]
            mdesc = row[3]
            mpic = row[4]
            mtime = row[5]
            mtype = row[6]
            print(mid, mname, msex, mdesc, mpic, mtime, mtype)

    def closeConnection(self):
        # 关闭连接等...
        self.cursor.close()
        self.conn.commit()
        self.conn.close()
```

### 作者类
```python
class Author(object):
    # 构造方法
    def __init__(self ,mname, msex , mdesc, mpic,mtime,mtype):
        self.mname = mname
        self.msex = msex
        self.mdesc = mdesc
        self.mpic = mpic
        self.mtime = mtime
        self.mtype = mtype

    # 对象方法
    def printInfo(self):
        print("当前对象信息：",  self.mname, self.msex, self.mtime, self.mtype, self.mpic, self.mdesc)
```

### 入口程序
```python
user_agent = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5)'
headers = {'User-Agent': user_agent}
session = requests.session()
sqlTool = MySqlTool()

def getAsleep():
    sleepTime = random.uniform(0, 2)
    print("休眠：", sleepTime)
    time.sleep(sleepTime)


def getUrlData( url ,mTime):
    # print("--------------------------------------")
    # print(url)
    page = session.get(url, headers=headers)
    soup = BeautifulSoup(page.text, 'lxml')
    # contents = soup.findAll('div', class='left')
    datas = soup.findAll(name='div', attrs={"class": "left"})
    for data in datas:
        items  = data.findAll( name= 'div',attrs={"class": "sonspic"})
        for item in items:
            urlDiv  =  item.find( name= 'div',attrs={"class": "divimg"})
            if not urlDiv is None:
                imgUrl= urlDiv.find(name= 'img')
                name= imgUrl['alt']
                pic= imgUrl['src']
                descP =  item.find( name= 'p',attrs={"style": " margin:0px;"})
                desc= descP.text
                pos = desc.index("►")  # 去掉无用的内容
                desc = desc[0:pos]

                author = Author(name, "", desc, pic, mTime, "")
                author.printInfo()
                sqlTool.addAuthor(author)
                print("--------------------------------------")


dict = {
    # "先秦": 4,
    # "两汉": 10,
    "魏晋": 13,
    "南北朝": 20,
    "隋代": 3,
    "唐代": 232,
    "五代": 4,
    "宋代": 583,
    "金朝": 10,
    "元代": 64,
    "明代": 279,
    "清代": 598
}


for mkey in dict:
    index = 0
    sum = dict[mkey]
    while index <= sum:
        url = "https://so.gushiwen.org/authors/Default.aspx?p=%d&c=%s" % (index, mkey)
        getUrlData(url, mkey)
        index = index + 1
        getAsleep()

sqlTool.closeConnection()   
```

### 喜马拉雅 json解析 + 文件下载
```
import requests
import urllib.request
import demjson

user_agent = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5)'
headers = {'User-Agent': user_agent}
session = requests.session()

def getAsleep():
    sleepTime = random.uniform(0, 2)
    print("休眠：", sleepTime)
    time.sleep(sleepTime)

def downAFile(fileName ,fileUrl ,downCallBack):
    downPath = 'F:\\小伊\\%s.m4a'%(fileName)
    print("正在下载：" ,fileName)
    urllib.request.urlretrieve(fileUrl, downPath ,downCallBack)

def downCallBack(downedCount, countSize, allSize):
    per = 100.0 * downedCount * countSize / allSize
    if per > 100:
        per = 100
    print('下载进度：  %.2f%%' % per)


def getUrlData( url ):
    page = session.get(url, headers=headers)
    jsonData = page.text

    bean1 = demjson.decode(jsonData)
    bean2 = bean1['data']
    bean3 = bean2['tracksAudioPlay']
    for data in bean3:
     name = data['trackName']
     # print("名字：" ,name)

     arrays= name.split("-")
     if len(arrays)< 2:
         arrays = name.split(" ")
     name = arrays[len(arrays)-1]
     src = data['src']
     downAFile(name ,src ,downCallBack)

urls = [
"https://www.ximalaya.com/revision/play/album?albumId=10564571&pageNum=1&sort=-1&pageSize=300" ,
"https://www.ximalaya.com/revision/play/album?albumId=10564571&pageNum=2&sort=-1&pageSize=300",
"https://www.ximalaya.com/revision/play/album?albumId=10564571&pageNum=3&sort=-1&pageSize=300",
"https://www.ximalaya.com/revision/play/album?albumId=10564571&pageNum=4&sort=-1&pageSize=300",
"https://www.ximalaya.com/revision/play/album?albumId=10564571&pageNum=5&sort=-1&pageSize=300"
]
for url in urls:
    getUrlData(url)
    getAsleep()
```

### 蜻蜓Fm 爬虫
爬取网易轻松一刻
```xml
```
