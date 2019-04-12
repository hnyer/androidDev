#  androidstudio
[androidstudio官网](https://developer.android.com/studio/index.html)

## Androidstudio 模板
```xml
ctrl + J  快速查看Live Templates
打开 *设置 -> Editor -> Live Templates* ，可以看到默认已经有很多 *Live Templates* 了 ，可以自己自定义。

导入原有的模板：
将文件替换即可 （C:\Users\kaifa4k\.AndroidStudio2.2 是你自己的安装路径）
C:\Users\kaifa4k\.AndroidStudio2.2\config\templates
```

### 我用的模板
```xml
<templateSet group="aivinTemplates">
  <template name="mswi" value="switch ($value$)&#10;{&#10;    case 1:&#10;        break;&#10;    default:&#10;}" description="switch快捷" toReformat="false" toShortenFQNames="true">
    <variable name="value" expression="annotated(&quot;annotation qname&quot;)" defaultValue="" alwaysStopAt="true" />
    <context>
      <option name="JAVA_CODE" value="true" />
      <option name="JAVA_STATEMENT" value="true" />
      <option name="JAVA_EXPRESSION" value="true" />
      <option name="JAVA_DECLARATION" value="true" />
      <option name="JAVA_COMMENT" value="true" />
      <option name="JAVA_STRING" value="true" />
      <option name="COMPLETION" value="true" />
    </context>
  </template>
</templateSet>
```


## androidstudio 插件
安装或更新后插件后都需要重启Androidstudio 。

### CodeGlance
代码快速定位 插件

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/osChina__CodeGlance.png)


### [Android-Resource-Usage-Count](https://plugins.jetbrains.com/plugin/9885-android-resource-usage-count)
显示 Android 资源使用的次数。

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/osChina__Android-Resource-Usage-Count.png)



### [翻译插件ECTranslation](https://github.com/Skykai521/ECTranslation)
原理是调用了有道词典的API 。可以自定义 快捷方式，我目前设置的是 alt + m
```xml
快捷键设置
Preferences -> Keymap -> 搜索Translate - > 右键 add Keyboard Shortcut. 输入你想要的快捷键。
```

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/osChina__ECTranslation.png)



### Android Parcelable code generator
自动生成 Parcelable接口相关的代码。



```java
public class FtpThumDownListInfo implements Parcelable{
    /**当前这个指针 指向的文件需要下载*/
    private int gotoDownFileIndex = 0;
    /**需要下载的文件列表- 这个列表会分批次增加 */
    private List<FTPFile> ftpNeddLoadList =null  ;
    /**下载好的文件的byte[]*/
    private List<FTPFile> ftpDowndBytesList =null  ;

    //以下代码是 用插件自动生成的  start
    protected FtpThumDownListInfo(Parcel in){
        this.gotoDownFileIndex = in.readInt();
        this.ftpNeddLoadList = new ArrayList<FTPFile>();
        in.readList(this.ftpNeddLoadList, FTPFile.class.getClassLoader());
        this.ftpDowndBytesList = new ArrayList<FTPFile>();
        in.readList(this.ftpDowndBytesList, FTPFile.class.getClassLoader());
    }

    /**
     * 内容接口描述，默认返回0就可以了
     */
    @Override
    public int describeContents(){
        return 0;
    }

    /**
     * 写数据进行保存
     */
    @Override
    public void writeToParcel(Parcel dest, int flags){
        dest.writeInt(this.gotoDownFileIndex);
        dest.writeList(this.ftpNeddLoadList);
        dest.writeList(this.ftpDowndBytesList);
    }

    /**
       实例化静态内部对象
     * 接口对象名必须为CREATOR.
     * 必须按成员变量声明的顺序读取数据
     */
    public static final Parcelable.Creator<FtpThumDownListInfo> CREATOR = new Parcelable.Creator<FtpThumDownListInfo>()  {
        @Override
        public FtpThumDownListInfo createFromParcel(Parcel source)  {
            return new FtpThumDownListInfo(source);
        }

        @Override
        public FtpThumDownListInfo[] newArray(int size)  {
            return new FtpThumDownListInfo[size];
        }
    };

    // 以下代码是 用插件自动生成的 end
}


//赋值
Bundle mBundle = new Bundle();
mBundle.putParcelable("picBean",picBean);  
intentToMatch.putExtras(mBundle);
//获取：
RecommendPicBean picBean = (RecommendPicBean)getIntent().getParcelableExtra("picBean");
```


### 无线调试 ADB WIFI
```xml
华为M2 ，无Root
1、 确保电脑和手机在同一个局域网
2、插上USB线 连上adb
3、adb tcpip 5555 ( 5555为端口号，可自由指定）
4、adb connect 192.168.x.x:5555
```
