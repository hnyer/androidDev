# git操作
## 配置全局信息
```xml
git config --global user.name "aivin666@qq.com"
git config --global user.email "aivin666@qq.com"
//查看当前用户信息
git config --list
```


## [添加个人私钥到网站](http://git.mydoc.io/?t=154712)
这种情况下无需配置密码 - window10下测试
```xml
1、生成对应的文件
ssh-keygen -t rsa -C 'aivin666@qq.com'
2、将 id_rsa.pub 里面的内容填写到网站上。
3、测试是否成功 。
ssh -T git@gitee.com
```

##  配置密码方式  Ubuntu下测试
https模式，每次提交都需要输入密码。
```xml
1、在一个新目录 初始化
git init

2、将远程代码拉倒本地
git pull https://gitee.com/hnyer/java_android.git

3、关联远程端仓库
master 是远程端的一个支路。
git remote add master https://gitee.com/hnyer/java_android.git
// git remote rm master , 删除关联

4、 将本地指定的版本推送到远程端指定的版本
的一个maser是远程的 ，第二个是本地的
git push -u master master

5、以后不用每次都用参数 u ,直接 git push 即可
```

## Ubuntu下 SSH 配置 码云
```xml
1、进入 /home/用户名/.ssh 目录下 生成公钥
ssh-keygen -t rsa -C "your_email@example.com"

2、将 id_rsa.pub 文件中的内容填入码云网站中 。
3、即可正常拉取 、提交
```
