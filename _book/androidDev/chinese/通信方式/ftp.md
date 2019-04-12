# ftp


## 第三方工具[ftp4j](http://www.sauronsoftware.it/projects/ftp4j/download.php)
### ftp登录
```java
@Override
public void run(){
     //标记是否登录成功
    boolean loginError = false ;
    try {
        mFTPClient.setType(FTPClient.TYPE_BINARY);
        //中文文件名 乱码处理
        mFTPClient.setCharset("GBK");
        //登录成功后FTP服务器返回的欢迎语句
        String[] welcome = mFTPClient.connect(FTP.HOST_IP, FTP.HOST_PORT);
        mFTPClient.login(FTP.HOST_USER, FTP.HOST_PASSWORD);
        mHandler.sendEmptyMessage(FTP.MSG_CMD_CONNECT_OK);
    }catch (Exception e) {
        loginError = true ;
    }
    if(loginError && FTP.mDameonRunning){
        mHandler.sendEmptyMessageDelayed(FTP.MSG_CMD_CONNECT_FAILED, 2000);
    }
}
```


### ftp删除文件（夹）
```java
@Override
public void run(){
    try {
        if (isDirectory)  {
            //删除文件夹
            mFTPClient.deleteDirectory(realivePath);
        } else {
            //删除文件
            mFTPClient.deleteFile(realivePath);
        }
        mHandler.sendEmptyMessage(FTP.MSG_CMD_DELE_OK);
    } catch (Exception ex) {
        mHandler.sendEmptyMessage(FTP.MSG_CMD_DELE_FAILED);
    }
}
```


### ftp断开连接
```java
if (mFTPClient != null){
    try {
        mFTPClient.disconnect(true);
    } catch (Exception ex) {
        ex.printStackTrace();
    }
}
```


### ftp文件下载
```java
if(! localFile.exists()){
    long fileSize = ftpFile.getSize() ;
    if(fileSize > 0){
        mFTPClient.download( ftpFile.getName(), new File(localFilePath),  
                             new DownloadFTPDataTransferListener(ftpFile.getSize()));
    }else{
        //文件异常的情况  下载发生异常the size of file muset be larger than zero.
    }
}
```

### ftp 获取文件列表
```java
@Override
public void run(){
    try {
        mFTPClient.changeDirectory(FTP.FTPSMAllPICDIR);
        FTPFile[] ftpFiles = mFTPClient.list();
        synchronized (mLock){
            mFileList.clear();
            //数组拷贝到list中去
            mFileList.addAll(Arrays.asList(ftpFiles));
        }
        mHandler.sendEmptyMessage(FTP.MSG_CMD_LIST_OK);
    } catch (Exception ex) {
        mHandler.sendEmptyMessage(FTP.MSG_CMD_LIST_FAILED);
    }
}//
```



### ftp 文件上传（进度条）
```java
try {
    File file = new File(path);
    mFTPClient.upload(file, new DownloadFTPDataTransferListener( file.length()));
} catch (Exception ex) {
    return false;
}
```

### ftp 连接守护进程
```java
//在子线程中定时判断
if (mFTPClient != null && !mFTPClient.isConnected()){
    try {
        //解决中文件名乱码问题
        mFTPClient.setType(FTPClient.TYPE_BINARY);
        mFTPClient.setCharset("GBK");
        mFTPClient.connect(FTP.HOST_IP, FTP.HOST_PORT);
        mFTPClient.login(FTP.HOST_USER, FTP.HOST_PASSWORD);
        Thread.sleep(FTP.MAX_DAMEON_TIME_WAIT);
    } catch (Exception ex) {
    }
}
```


### FTP 传输进度 回调
```java
public class DownloadFTPDataTransferListener implements  FTPDataTransferListener{
    /**已经传送量*/
    private int totolTransferred = 0;
    /**数据总量*/
    private long fileSize = -1;

    public DownloadFTPDataTransferListener(long fileSize){
        if (fileSize <= 0) {
            throw new RuntimeException( "the size of file muset be larger than zero.");
        }
        this.fileSize = fileSize;
    }

    @Override
    public void aborted()  {}

    @Override
    public void completed(){  }

    @Override
    public void failed()  {  }

    @Override
    public void started()  {  }

    @Override
    public void transferred(int length){
        totolTransferred += length;
        float percent = (float) totolTransferred / this.fileSize;
    }
}
```

### 使用场景
```java
FtpManager ftpManager = new FtpManager();
//然后 ftp 的相关操作 要放到子线程中去执行。 建议使用线程池。
```
