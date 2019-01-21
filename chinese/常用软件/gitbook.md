# gitbook
## windows 安装gitbook
```xml
安装gitbook过程中请优先连接VPN，如果没有请考虑更换淘宝源等方式。
安装过程 可以参考 https://segmentfault.com/a/1190000005859901

npm install gitbook -g
npm install -g gitbook-cli
gitbook -V
```


## gitbook常用的使用命令
```xml
// 安装依赖包
gitbook install
//新建定义好的文件
gitbook init
// 本地服务器测试
gitbook serve
// 编译成html格式
gitbook build
```


## 安装错误
如果遇到 “ Error: ENOENT: no such file or directory ... ” 这个错误 ，
```xml
把.gitbook\versions\3.2.2\lib\output\website\copyPluginAssets.js
修改成   confirm：false ！！！
```
