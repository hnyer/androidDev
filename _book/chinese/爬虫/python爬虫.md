# 爬虫
Java是强类型，编译型语言。
Python是若类型，解释型语言。

```xml
python -V  //查看当前版本   python版本
import sys
print (sys.version)
```

##  推荐IDE PyCharm


## pip
pip 是Python的软件包管理系统，Python语言自带的命令行工具，它可以安装和管理第三方软件包。
### pip 安装库
```xml
pip install requests
http://www.lfd.uci.edu/~gohlke/pythonlibs/
```


## urllib、urllib2
在Python3已拆分更名为urllib.request和urllib.error
```xml
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

## BeautifulSoup
```xml
pip install beautifulsoup4
```


## 中文乱码
```xml
1、先用chardet检测读取到的内容的编码
chardet.detect(content)
2、转换成合适的编码格式
html = unicode(content, "gb2312").encode("utf8")
```

## 简单demo
### 权谋网
# 权谋网
资料来源 [权谋网](http://www.mou5.com/)
爬虫程序很简单，并没有优化。
####  获取文章列表
```python
import requests
from bs4 import BeautifulSoup

user_agent = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5)'
headers = {'User-Agent': user_agent}
session = requests.session()

def getAndSaveUrl( url):
    print(url)
    page = session.get(url, headers=headers)
    soup = BeautifulSoup(page.text, 'lxml')
    contents = soup.findAll('div', id='contentleft')
    for child in contents:
        item = child.findAll('h2')
        for hValue in item:
            aValue = hValue.find('a')
            title = aValue.string
            url = aValue['href']
            print(title)
            f = open('D:/网址.txt', 'a')
            f.write('\n' + url)

# sum大小 请根据网站最新页数调整
sum = 181
index =1
while index <= 181:
    url="http://www.mou5.com/page/%d"%(index)
    getAndSaveUrl(url)
    index = index + 1
```

#### 获取保存文章内容
```python
import random
import requests
import time
from bs4 import BeautifulSoup
import re

user_agent = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5)'
headers = {'User-Agent': user_agent}
session = requests.session()

localDir ="C:\\test\\files\\"

def getAndSaveUrl( url):
    # print("抓取 "+url)
    page = session.get(url, headers=headers)
    soup = BeautifulSoup(page.text, 'lxml')

    contents = soup.findAll('div', id='contentleft')
    for child in contents:
        item = child.findAll('h2')
        title= item[0].string
        # 去除标题中的特殊字符
        title = re.sub("[\s+\.\!\/_,$%^*(+\"\']+|[+——！，。?|？、~@#￥%……&*（）]+", "", title)
        filePath = localDir  +title+ ".md"
        f = open(filePath, 'w')
        print(filePath+"创建成功")

        # 以下保存正文
        f = open(filePath, 'a', encoding='utf-8')
        f.write('#  ')
        f.write(title+ ' \n ')
        f.write('```xml \n ')

        pvalues = child.findAll("span" ,attrs={'style': 'font-size:18px;'})
        for pvalue in pvalues:
            content = pvalue.string
            if not content is None:
                # print(content)
                f = open(filePath, 'a' ,encoding='utf-8')
                f.write( content+'\n' )
        f.write('``` \n ')
        f.close()
    print("完成 "+url)


# 开始运行 抓取 保存
fileDest =localDir+"网址.txt"
f=open(fileDest,'r')
lines=f.readlines()
sum = len(lines)
index = 0;
for line in lines:
    index= index+1
    sleepTime = random.uniform(0, 2)
    time.sleep(sleepTime)  # 休眠 1秒
    print("进度%d/%d   ,  休眠%f秒" % (index, sum, sleepTime))
    # 去掉末尾的换行符
    url = line.strip('\n')
    try:
     getAndSaveUrl(url)
    except:
        filePath = localDir  + "errorLog.txt"
        f = open(filePath, 'a', encoding='utf-8')
        print("!!! 抓取失败 " + url)
        f.write(url+'\n')
```


#### 生成 SUMMARY 文件
```python
import os.path

filePath =  "C:\\test\\SUMMARY.md"
f = open(filePath, 'a', encoding='utf-8')

f.write( '# Summary\n')
f.write( '\n')
f.write( '\n')
f.write( '* [项目介绍](README.md)')
f.write( '\n')
f.write( '* [权谋网](chinese/权谋网/权谋网.md)')
f.write( '\n')


rootdir ="C:\\test\\files\\"

list = os.listdir(rootdir)
for file in list:
    fileBaseName =  os.path.basename( file)
    array = fileBaseName.split(".")
    f.write( '    * [')
    f.write( array[0])
    f.write('](chinese/权谋网/')
    f.write(fileBaseName)
    f.write( '\n')
```
