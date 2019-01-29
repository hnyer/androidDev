# 通信方式
## http 、https 方式
```xml
1、http是基于 TCP/IP 的应用层协议 。
2、 采用 “一问一答” 模式 ，并不会保持长连接。
3、HTTP允许传输任意类型的数据对象。传输的类型由Content-Type加以标记。
```

### Content-type
[HTTP Content-type 手册](http://tool.oschina.net/commons)

文件扩展名	| Content-Type
-|-
.*（ 二进制流，不知道下载文件类型）|	application/octet-stream
.txt	|text/plain
.xml	|text/xml
.css	|text/css
.html	|text/html
.js	|application/x-javascript


以下是RFC1867协议为HTTP协议的基础上|-为INPUT标签增加了file属性

文件扩展名	| Content-Type
-|-
无文件的表单提交|application/x-www-form-urlencoded
有文件的表单提交|multipart/form-data

没查到何人何时 添加支持“json提交格式 ”的。

文件扩展名	| Content-Type
---|---
json格式的请求|application/json


### 建立、断开 http连接
http建立连接，底层是通过 Tcp协议 经过了3次握手、断开经过4次挥手。具体请查看tcp的相关知识。

###  TCP/IP协议族
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802071433_osChina_tcp_ip协议族.png)


## https请求
即使采用了 HTTPS，也可以实现中间人攻击，或者进行数据解密等，但是，目前 HTTPS 目前使用的加密算法需要巨大的计算量才能破解
```xml
HTTPS 的目的主要有三个，
一个是数据的保密性，你给别人送个邮包，但邮包是打了封印的，快递员不知道包里装的是啥。
第二是数据的完整性，快递员不知道邮包是啥，但可以掉包啊，HTTPS 发现了掉包的数据，可以拒收。
第三就是身份验证，HTTPS 可以保证邮包被正确的送到指定的接收方，而不是第三方。
```

### https相关术语
专业名词 | 解释 |其他
-|-
SSL（Secure Sockets Layer）|  |网景公司设计的
单向证书认证|任何客户端都可以访问服务器|
双向证书认证|被允许的客户端才能访问服务器|
对称加密|加密和解密时使用的是同一个秘钥|
非对称加密|用公钥加密，私钥解密|
公钥|负责加密|在网络上传输
私钥|负责解密| 本地保管，别人无法获取
