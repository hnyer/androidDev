# python 爬虫
## 古诗文网 爬取作者信息 (静态页面)
```text
// 数据库帮助类
class MySqlTool(object):
    def __init__(self):
        self.conn = pymysql.connect(host='xx.xx.xx.xx65', port=3306, user='xx', passwd='xx', db='xx', charset='UTF8')
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
 
// 作者类
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

// 入口程序
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

## 喜马拉雅 json解析 + 文件下载
```text
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
 
## 权谋网 文章
```text
// 获取文章列表
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
 
//  获取保存文章内容
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
 
## 每日毒鸡汤
```text
public void getWeatherOfFoshanNanhai()  {
       String url=  "https://lwdjt.com";
       String filePath ="c://test/毒鸡汤.txt" ;
       File file = new File(filePath);
       try {
           if(!file.exists()){
               file.createNewFile();
           }
       } catch (IOException e) {
           e.printStackTrace();
       }
       FileWriter writer = new FileWriter(filePath, true);

       int count = 1;
       while (count <=100000){
           Document doc = Jsoup.connect(url).timeout(2000).get();
           Elements tworem = doc.select("main.tworem");
           String str = tworem.text() ;
           // 繁体转简体
           String result = ZhConverterUtil.convertToSimple(str);
           System.out.println(count+"、"+result);
           writer.write("\"");
           writer.write(result  );
           writer.write("\","  );
           writer.write(  "\r\n");
           writer.flush();
           count++ ;
       }
       writer.close();
   }
```
