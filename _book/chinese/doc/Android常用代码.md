# 常用代码片段

## 数组操作
### list去重
将重复值去掉，同时不能破坏现有的顺序。
```text
private  List<String> removeDuplicate(List<String> list){
  Set<String> set = new HashSet<String>();
  List<String> newList = new ArrayList<String>();
  for (String str : list) {
    // 借助set的特性
    if (set.add(str)) {
      newList.add(str);
    }
  }
  return newList;
}
```


## String 操作
### 字符串切割
```text
String [] array= myString.split("[.]");
```

分割字符| 表达
-|-
.|"[.]"
&#124;|"\\\\&#124;"
*|"\\\\*"
\|"\\\\\\\\"
[|"\\\\["
]|"\\\\]"

### String 简单格式化
```text
return String.format("bookId= %s, bookName=%s]", bookId, bookName);
return String.format("%.1f",  value); // float double 类型
```

### SpannableString 关键字匹配
```text
public SpannableString showKeyWord(String str, String keyWord) {
    str = str == null ? "" : str;
    keyWord = keyWord == null ? "" : keyWord;

    Pattern pattern = Pattern.compile(keyWord);
    SpannableString spannableString = new SpannableString(str);
    Matcher matcher = pattern.matcher(spannableString);
    while (matcher.find()) {
        int start = matcher.start();
        int end = matcher.end();
        spannableString.setSpan(new ForegroundColorSpan(Color.RED), start, end, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
    }
    return spannableString;
}
tvShowInfo.setText(showKeyWord(info, "测试"));
```


### strig 和 byte[] 互相转换
```text
byte[] bytes = "hello world".getBytes(StandardCharsets.UTF_8);
String hello = new String(bytes , StandardCharsets.UTF_8);
```

## 代码超时
函数超时、 执行超时、等待超时、连接超时

```text
final ExecutorService exec = Executors.newFixedThreadPool(1);
Callable<String> call = new Callable<String>() {
	public String call() throws Exception {
		// 耗时操作...
		Thread.sleep(1000 * 3);
		return "执行结果(成功或失败)";
	}
};

try {
	Future<String> future = exec.submit(call);
	// 超时时间 设置
	String result = future.get(1000 * 5, TimeUnit.MILLISECONDS);  
	System.out.println("执行未超时:" + result);
} catch (TimeoutException ex) {
	System.out.println("处理超时");
	ex.printStackTrace();
} catch (Exception e) {
	System.out.println("处理失败.");
	e.printStackTrace();
}
	exec.shutdown();
}
```


## 对象排序
```text
Collections.sort(mBeanList, new Comparator<MyBean>() {
        @Override
        public int compare(MyBean bean1, MyBean bean2) {
            // 按照对象属性排序
          return = bean1.xx - bean2.xx;
        }
    });
```


## 数组操作
### 数组相等
```text
Arrays.equals(myArray, NSS_MSG_TYPE_GET_MAIN_STREAM);
```

### 数组打印
```text
System.out.println(Arrays.toString(myArray));
```

### 数组扩容
```text
public  String [] getNewArrayWithMoreSpaceString(
String [] oldArray ,String [] increceData){
   String [] lastArray=new String[oldArray.length+increceData.length];
   //数组复制
   System.arraycopy(oldArray, 0, lastArray, 0, oldArray.length);
   System.arraycopy(increceData, 0, lastArray, oldArray.length+1, increceData.length);
   return   lastArray ;
}

/**
 * 多个数组合并
 */
byte[] concatAll(byte[] first, byte[]... rest) {
    int totalLength = first.length;
    for (byte[] array : rest) {
        totalLength += array.length;
    }
    byte[] result = Arrays.copyOf(first, totalLength);
    int offset = first.length;
    for (byte[] array : rest) {
        System.arraycopy(array, 0, result, offset, array.length);
        offset += array.length;
    }
    return result;
}
```

## byte[]相关
### byte[]和基础数据互转
```xml 
例如一个32位long数据，0x12345678:

             0x12345678
高字节   ----------------->   低字节

内存低地址 ----------------->  内存高地址
大端模式	0x12	0x34	0x56	0x78      //符合人的直观认识
小端模式	0x78	0x56	0x34	0x12
```

```text
// int 转为 byte
byte intTo1ByteHex(int intVaue ){
    return Integer.valueOf(intVaue & 0xff).byteValue();
}

// byte 转 int
int bytes1ToInt(byte  src) {
    return src  & 0xFF;
}

// int 转 byte[4]  ,大端模式
byte[] intTo4ByteBig(int n) {
    byte[] b = new byte[4];
    b[3] = (byte) (n & 0xff);
    b[2] = (byte) (n >> 8 & 0xff);
    b[1] = (byte) (n >> 16 & 0xff);
    b[0] = (byte) (n >> 24 & 0xff);
    return b;
}

// int 转 byte[4],小端模式
byte[] intTo4ByteLittle(int n) {
    byte[] b = new byte[4];
    b[0] = (byte) (n & 0xff);
    b[1] = (byte) (n >> 8 & 0xff);
    b[2] = (byte) (n >> 16 & 0xff);
    b[3] = (byte) (n >> 24 & 0xff);
    return b;
}


// byte[4] 转int ，小端模式
int bytes4ToIntLittle(byte[] bytes ) {
    int int1=bytes[0]&0xff;
    int int2=(bytes[1]&0xff)<< 8 ;
    int int3=(bytes[2]&0xff)<< 16;
    int int4=(bytes[3]&0xff)<<24;
    return int1|int2|int3|int4;
}


// byte[4] 转 int ，大端
int bytes4ToIntBig(byte[] bytes ) {
    int int1=bytes[3]&0xff;
    int int2=(bytes[2]&0xff)<<8;
    int int3=(bytes[1]&0xff)<<16;
    int int4=(bytes[0]&0xff)<<24;
    return int1|int2|int3|int4;
}


// short 转 byte[2] ，大端模式
byte[] shortTo2ByteBig(short n) {
    byte[] b = new byte[2];
    b[1] = (byte) (n & 0xff);
    b[0] = (byte) (n >> 8 & 0xff);
    return b;
}


// short 转 byte[2] ，小端模式
byte[] shortTo2ByteLittle(short n) {
    byte[] b = new byte[2];
    b[0] = (byte) (n & 0xff);
    b[1] = (byte) (n >> 8 & 0xff);
    return b;
}

// byte[2] 转 short ,小端模式
short byte2ToShortLittle(byte[] b) {
    return (short) (((b[1] << 8) | b[0] & 0xff));
}

// byte[2] 转 short ,大端模式
short byteToShortBig(byte[] b) {
    return (short) (((b[0] << 8) | b[1] & 0xff));
}
 
// byte[2] 转short ，大端
short bytes2ToShort_Big(byte[] bytes) {
    return ByteBuffer.wrap(bytes).order(ByteOrder.BIG_ENDIAN).getShort();
}

// byte[2] 转 short ，小端
short bytes2ToShort_Little(byte[] bytes) {
    return ByteBuffer.wrap(bytes).order(ByteOrder.LITTLE_ENDIAN).getShort();
}


/**
 * byte[4] 转 float
 * @param littleEndian  小端模式: rue , 大端模式: false
 */
private float bytes4ToFloat(byte[] bytes, boolean littleEndian) {
    ByteBuffer buffer = ByteBuffer.wrap(bytes,0,4);
    if(littleEndian){
        buffer.order(ByteOrder.LITTLE_ENDIAN);
    }else{
        buffer.order(ByteOrder.BIG_ENDIAN);
    }
    return   buffer.getFloat();
}


/**
 *  float 转 byte[4 ]
 * @param littleEndian 小端模式: rue , 大端模式: false
 */
private byte[] float2Bytes4(  float valueF, boolean littleEndian) {
    ByteBuffer bbuf = ByteBuffer.allocate(4);
    bbuf.putFloat(valueF);
    byte[] bytes = bbuf.array();  //默认大端

    if(littleEndian){
        int size= bytes.length ;
        byte[ ] rollover = new byte[size] ;

        for(int i= 0 ; i<size ;i++){
            rollover[i] = bytes[size-1 - i] ;
        }
        bytes = rollover ;
    }
    return  bytes ;
}


// double 转 byte[8] ,小端模式
byte[] doubleTo8Bytes_Little(double d){
    long value = Double.doubleToLongBits(d);
    byte[] b = new byte[8];
    b[7] = (byte)  (0xff & (value >> 56));
    b[6] = (byte)  (0xff & (value >> 48));
    b[5] = (byte)  (0xff & (value >> 40));
    b[4] = (byte)  (0xff & (value >> 32));
    b[3] = (byte)  (0xff & (value >> 24));
    b[2] = (byte)  (0xff & (value >> 16));
    b[1] = (byte)  (0xff & (value >> 8));
    b[0] = (byte)  (0xff & value);
    return b;
}


// double 转 byte[8] ,大端模式
byte[] doubleTo8Bytes_Big(double d){
    long l = Double.doubleToLongBits(d);
    byte[] b = new byte[8];
    b[0] = (byte)  (0xff & (l >> 56));
    b[1] = (byte)  (0xff & (l >> 48));
    b[2] = (byte)  (0xff & (l >> 40));
    b[3] = (byte)  (0xff & (l >> 32));
    b[4] = (byte)  (0xff & (l >> 24));
    b[5] = (byte)  (0xff & (l >> 16));
    b[6] = (byte)  (0xff & (l >> 8));
    b[7] = (byte)  (0xff & l);
    return b;
}

/**
 * byte[8] 转 double
 */
double bytes8ToDouble(byte[] bytes, boolean littleEndian) {
    ByteBuffer buffer = ByteBuffer.wrap(bytes,0,8);
    if(littleEndian){
        buffer.order(ByteOrder.LITTLE_ENDIAN);
    }else{
        buffer.order(ByteOrder.BIG_ENDIAN);
    }
    long l = buffer.getLong();
    return Double.longBitsToDouble(l);
}
```

###  获得校验位
```text
/*
只保留低八位，高位舍去。
P1=s/256; //整除高8位
P0=s%256; //取余是低8位
*/
public static  byte[] getVerifyValue(byte [] lastBytes){
    byte checksum =  0;
    for (int i = 0; i <= lastBytes.length-2; i++) {
        checksum += lastBytes[i];
    }
    checksum = (byte) (checksum % 256) ;
    lastBytes[lastBytes.length-1] = checksum;
    return  lastBytes ;
}

// BCC 校验位算法
byte BCC_CHECK(byte[] data, int bcccheck_lenth) {
    byte bcc_result = 0;
    for (int i = 0; i < bcccheck_lenth; i++) {
        bcc_result ^=data[i];
    }
    return bcc_result;
}
    
// 一个byte转换byte[8]，数组每个值代表bit
public byte[] test(byte b) {
    byte[] array = new byte[8];
    for (int i = 7; i >= 0; i--) {
        array[i] = (byte)(b & 1);
        b = (byte) (b >> 1);
    }
    return array;
}

// byte[] 转换成十六进制字符串 ,方便打印查看
String byte2HexStr(byte[] b){
    String stmp ;
    StringBuilder sb = new StringBuilder("");
    for (int n = 0; n < b.length; n++) {
        stmp = Integer.toHexString(b[n] & 0xFF);
        sb.append((stmp.length() == 1) ? "0" + stmp : stmp);
        sb.append(" ");
    }
    return sb.toString().toUpperCase().trim();
}

```




## 文件操作
### 获得指定文件的byte数组
```text
public  byte[] getBytesFromFile(File file){  
      byte[] buffer = null;  
      try {  
        if(!file.exists()){
          file.createNewFile();
        }
          FileInputStream fis = new FileInputStream(file);  
          ByteArrayOutputStream bos = new ByteArrayOutputStream(1000);  
          byte[] b = new byte[1000];  
          int n;  
          while ((n = fis.read(b)) != -1) {  
              bos.write(b, 0, n);  
          }  
          fis.close();  
          bos.close();  
          buffer = bos.toByteArray();  
      } catch ( Exception e) {  
          e.printStackTrace();  
      }  
      return buffer;  
  }
```

### 根据byte数组，生成文件
```text
public void getFileFromBytes(byte[] bfile, File file ,boolean isAppend){  
    BufferedOutputStream bos = null;  
    FileOutputStream fos = null;  
    try {  
        fos = new FileOutputStream(file ,isAppend);  
        bos = new BufferedOutputStream(fos );  
        bos.write(bfile);  
    } catch (Exception e) {  
        e.printStackTrace();  
    } finally {  
        if (bos != null) {  
            try {  
                bos.close();  
            } catch (IOException e1) {  
                e1.printStackTrace();  
            }  
        }  
        if (fos != null) {  
            try {  
                fos.close();  
            } catch (IOException e1) {  
                e1.printStackTrace();  
            }  
        }  
    }  
}
```

### 保存String到本地
```text
public  boolean saveTxtToSdWithAppend(String messgeStr,  String filePath, boolean isAppend){
     File file = new File(filePath);
     try {
         file.createNewFile();
     } catch (IOException e) {
         e.printStackTrace();
     }

     boolean resultFlage = true;
     try {
         // 打开一个写文件器，是否追加形式
         FileWriter writer = new FileWriter(filePath, isAppend);
         writer.write(messgeStr + "\r\n");
         writer.close();
     } catch (IOException e) {
         resultFlage = false;
         e.printStackTrace();
     }
     return resultFlage;
 }
```

### 从txt(小文件) 获取String
```text
public  String getStringFromTxt(File file) {
    StringBuffer sb = new StringBuffer();
    try {
        BufferedReader rd = new BufferedReader(new FileReader(file));
        String s = rd.readLine();
        while (null != s) {
            sb.append(s);
            s = rd.readLine();
        }

    } catch (FileNotFoundException e) {
        e.printStackTrace();   
    } catch (IOException e) {
        e.printStackTrace();
    }
    return sb.toString();
}
```


### 按行读取(较大文件) 、解决乱码问题
```text
public void read(String filePath, String logPath) throws  Exception {
  RandomAccessFile raf = new RandomAccessFile(filePath, "r");
  // 设置指针的位置为文件的开始部分
  raf.seek(currentInex);
  long totalLenth = raf.length();
  while (currentInex <= totalLenth) {
    String stringValue = new String(raf.readLine().getBytes("ISO-8859-1"), "utf-8");
    currentInex = raf.getFilePointer();
    raf.close();
  }
}
```


### 递归 遍历文件夹
```text
public void  getFileList(String strPath) {
      File dir = new File(strPath);
      File[] files = dir.listFiles();  
      if (files != null) {
          for (File file : files) {
              if (file.isDirectory()) {  
                  getFileList(file.getAbsolutePath());  
              } else {
                  // 文件
              }
          }
      }
  }
```

## 时间、日期相关
### 时间间隔
```text
public static String getConversationDateString(String result) throws  Exception{
  if(!result.equals("")){
    SimpleDateFormat sdf = new SimpleDateFormat("MM-dd HH:mm");
    SimpleDateFormat sdfDay = new SimpleDateFormat("MM-dd");
    String now = sdf.format(new Date());
    Date oldTime = null;
    Date newTime = null;
    oldTime = sdf.parse(result);
    newTime = sdf.parse(now);

    long time = newTime.getTime() - oldTime.getTime();
    int day;
    String[] olds = sdfDay.format(oldTime).split("-");
    String[] news = sdfDay.format(newTime).split("-");

    if(news[0].equals(olds[0])){
      day = Integer.valueOf(news[1]) - Integer.valueOf(olds[1]);
    }else{
      Calendar cal = Calendar.getInstance();  
      //月份设置要减1，所以设置1月就是1-1，设置2月就是2-1，如此类推  
      cal.set(Calendar.MONTH, 1-1);  
      cal.add(Calendar.MONTH, -(Integer.valueOf(news[0]) - Integer.valueOf(olds[0])));  
      //得到上一个月最最后一天日期(31/30/29/28)  
      int oldDay=cal.getActualMaximum(Calendar.DAY_OF_MONTH) - Integer.valueOf(olds[1]);  
      day = oldDay + Integer.valueOf(news[1]);
    }

    if(time <= 24 * 60 * 60 * 1000){
      if(time <= 60 * 1000){
        return "刚刚";
      }else if (time <= 60 * 60 * 1000) {
        result = (time/60/1000) + "分钟前";
      } else {
        result = (time/60/60/1000) + "小时前";
      }
    }else if(day == 1){
      result = "昨天";
    }else if(day == 2){
      result = "前天";
    }else if(day < 7){
      result = day + "天前";
    }else {
      result = sdfDay.format(oldTime);
    }
  }
  return result;
}
```


### 秒转为 小时+分钟+秒
```text
/**
 * 秒转为 小时+分钟+秒 显示
 * @param  second 秒
 */
public static String getHourAndMinute(int second , StringBuilder sb ) {
    if(sb==null){
        sb=new StringBuilder();
    }
    sb.delete(0,sb.length()) ;

    int hour = second / 3600;
    int minute = (second - hour * 3600) / 60;
    second = second - hour * 3600 - minute * 60;

    if(hour>0){
        sb.append(hour).append("h") ;
    }

    if(minute>0){
        sb.append(minute).append("min") ;
    }

    if(second>=0){
        sb.append(second).append("s") ;
    }
    return sb.toString();
}
```


### 日期格式化
```text
/**
 * "yyyy-MM-dd HH:mm:ss"
 *  将 "1970-1-1 12:25:01"  字符串 转为 long 类型的 时间
 */
public long getMillisecondsTimeFromStrYyyyMMddHHmmss(String timeStr){
    DateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss" ,Locale.getDefault());
    Date date  = null;
    try {
        date = format.parse(timeStr);
    } catch (ParseException e) {
        e.printStackTrace();
    }
    return date==null ? 0: date.getTime() ;
}

public String getFormatDateStringYyyyMMddHHmmssFromLongTime(long mills) {
    Date date = new Date(mills);
    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss",Locale.getDefault());
    return simpleDateFormat.format(date);
}
```


### 获取今天最后一秒的时间
```text
public static long getlongTimeOfTodayYyyyMMddHHmmss(){
    Calendar calendar = Calendar.getInstance();
    calendar.set(Calendar.HOUR_OF_DAY, 23);
    calendar.set(Calendar.MINUTE, 59);
    calendar.set(Calendar.SECOND, 59);
    return  calendar.getTimeInMillis(); //今天
}
```

## 求夹角
### 根据正切公式
```text
/**根据两个点获取 斜率*/
public static double getXl(MyPointBean bean1, MyPointBean bean2) {
    double xlValue = 0;
    xlValue = (bean2.getY() - bean1.getY()) / (bean2.getX() - bean1.getX());
    return xlValue;
}

/**根据2条线的斜率 获得之间的夹角*/
public static double getAngle(double xl_1, double xl_2) {
    double angleValue = 0;
    // 反正切
    angleValue = Math.atan((xl_2 - xl_1) / (1 + xl_1 * xl_2));
    // 将弧度转为度数
    return Math.toDegrees(angleValue);
}
```

## 遍历map 遍历set
```text
Map<String ,Integer>  map=new HashMap();
//方法一 ，需要遍历 两次     
for (Object object : map.keySet()) {
 String key = (String) object;
 Integer value = (Integer) map.get(key);
}

// 方法二， 只需遍历一次 （推荐）
Set<Entry<String, Integer>>  entrySet = map.entrySet();
for (Entry<String, Integer> object : entrySet){
 Object key = object.getKey();
 Object value = object.getValue();
}

//遍历set
Set<String> set=new HashSet();
Iterator<String> iterator=set.iterator();
while (iterator.hasNext()) {
  String obj = iterator.next();
}
```


## List 分页
```text
https://gitee.com/Aivin_CodeShare/android_tool_code/raw/master/ListPageUtil.java

这里有用到 List的 subList() 函数。
不过要注意一个问题：
subList 返回的是 ArrayList的内部类 SubList。
对子列表或原列表的操作，会影响到彼此。
而且如果对原列表增删操作后，若对子列表做增删改查，都会报错崩溃。

所以我们一般会重新新建一个 
subList = new ArrayList<>(list.subList(pageIndex * pageSize, listSize));
```




## 随机数 Random
Math.Random() 范围是 [0,1)

###  生成 [startNum,endNum)
```text
public int getRadomInt(int startNum, int endNum){
  return new Random().nextInt(endNum - startNum) + startNum;
}
```

### 生成 [startNum,endNum]
```text
public int getRadomInt2(int startNum, int endNum){
  return new  Random().nextInt(endNum - startNum + 1) + startNum;
}
```

## 文件解压缩
zip解压缩是基于 pache的 ant.jar 实现 。

```text
/**
 * 解压耗时操作应该在子线程中进行。
 */
public class ZipUtil{
		private byte[] buf;
	  public ZipUtil(){
	      this.buf = new byte[ 1024*4];
	  }

    /**
     * @param zipFileAbsulutPaht 压缩文件的绝对路径  xxx/xxx.zip
     * @param destFileDir　　　解压到的目录　
     */
    public void unZip(String zipFileAbsulutPaht, String destFileDir)  {
        FileOutputStream fileOut;
        File file;
        InputStream inputStream;

        try {
            //生成一个zip的文件
            ZipFile  zipFile = new ZipFile(zipFileAbsulutPaht);
            //遍历zipFile中所有的实体，并把他们解压出来
            for (@SuppressWarnings("unchecked") Enumeration<ZipEntry> entries = zipFile.getEntries(); entries.hasMoreElements();)  {
                ZipEntry entry =  entries.nextElement();
                //生成他们解压后的一个文件
                String tempNameFile=entry.getName() ;
                file = new File(destFileDir+File.separator+tempNameFile.replace("\\", "/"));

                if (entry.isDirectory())  {
                    file.mkdirs();
                } else {
                    File parent = file.getParentFile();
                    if (!parent.exists())  {
                        parent.mkdirs();
                    }
                    //获取出该压缩实体的输入流
                    inputStream = zipFile.getInputStream(entry);
                    fileOut = new FileOutputStream(file);
                    int length = 0;
                    //将实体写到本地文件中去
                    while ((length = inputStream.read(this.buf)) > 0)  {
                        fileOut.write(this.buf, 0, length);
                    }
                    fileOut.close();
                    inputStream.close();
                }
            }
            zipFile.close();
        } catch (IOException ioe) {
            ioe.printStackTrace();
        }
    }
}

// 使用
new ZipUtil().unZip(mnt/xxx/xxx.zip , mnt/xx/xx/);
```


##  res/raw 和 assets 读取
```text
// res/raw和assets的相同点：
1、两者目录下的文件在打包后会原封不动的保存在apk包中，不会被编译成二进制。

//  res/raw和assets的不同点：
1、res/raw中的文件会被映射到R.java文件中，访问的时候直接使用资源ID即R.id.filename；
2、assets文件夹下的文件不会被映射到R.java中，访问的时候需要AssetManager类。
3、res/raw不可以有目录结构；
4、而assets则可以有目录结构，也就是assets目录下可以再建立文件夹

/**
 * 获取 assets 目录下的 json文件数据 <br>
 * String distsData = new HXGetJsonDataTool().getJsonFromAssets(activity, "testjson.txt");
 */
public static String getJsonFromAssets(Context context, String fileName) {
    StringBuilder stringBuilder = new StringBuilder();
    try {
        AssetManager assetManager = context.getAssets();
        BufferedReader bf = new BufferedReader(new InputStreamReader(assetManager.open(fileName)));
        String line;
        while ((line = bf.readLine()) != null) {
            stringBuilder.append(line);
        }

    } catch (IOException e) {
        e.printStackTrace();
    }
    return stringBuilder.toString();
}
```


## URLEncoder 中文
### 判断是否有中文
```text
URLEncoder.encode(str ,"UTF-8"));

public static boolean isChineseChar(String str){
    boolean temp = false;
    Pattern p=Pattern.compile("[\u4e00-\u9fa5]");
    Matcher m=p.matcher(str);
    if(m.find()){
        temp =  true;
    }
    return temp;
}
```

### 正则换URL字符串里面的汉字部分
```text
public static String encode(String str, String charset)  {
    Pattern p = Pattern.compile("[\u4e00-\u9fa5]");
    Matcher m = p.matcher(str);
    StringBuffer b = new StringBuffer();
    while (m.find()) {
        m.appendReplacement(b, URLEncoder.encode(m.group(0), charset));
    }
    m.appendTail(b);
    return b.toString();
}
```



## 其他

### 判断是否是闰年
```text
if (n%400==0 ||n%4==0 && n%100!=0) {
 //闰年
}
```

### 双击退出 、按键监听
```text
private long lastTime = 0 ;
private void exitActivity( ) {
    if (System.currentTimeMillis() - lastTime < 2000L) {
        this.finish() ;
    } else {
        lastTime = System.currentTimeMillis() ;
        toastMsg("再次点击退出");
    }
}

KeyEvent.KEYCODE_BACK         // 	返回键
KeyEvent.KEYCODE_VOLUME_UP   // 音量增加键
KeyEvent.KEYCODE_VOLUME_DOWN // 音量减小键
KeyEvent.KEYCODE_VOLUME_MUTE //扬声器静音键
KeyEvent.KEYCODE_MENU         // 菜单键
KeyEvent.KEYCODE_CALL         // 拨号键
KeyEvent.KEYCODE_ENDCALL        // 挂机键
KeyEvent.KEYCODE_HEADSETHOOK   // 耳机键
KeyEvent.KEYCODE_MEDIA_PLAY_PAUSE // 播放 / 暂停
KeyEvent.KEYCODE_MEDIA_STOP   // 多媒体键 停止
KeyEvent.KEYCODE_MEDIA_PAUSE// 多媒体键 暂停
``` 

### 不定参数
```text
//相当于是二维数组，arg中每个空间保存一个数组
public void method(int[]...arg);   
```

### 倒计时
```text
简单场景就使用Chronometer。
复杂场景使用网上很多开源的，比如小米时钟、锤子时钟中的实现思路。
```

### 选中效果
xxx.setSelected(false);
```text
 <?xml version="1.0" encoding="UTF-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/checkout_middle" android:state_selected="true"/>
    <item android:drawable="@drawable/call_grren" android:state_focused="false" android:state_pressed="true"/>
    <item android:drawable="@drawable/check_out_left"/>
</selector>
```


### 点击效果
```text
方法一，可以采取 xml 配置的方式。
方法二，可以采用 Drawable 来实现 。
重写 ImageView 的方法 
public boolean onTouchEvent(MotionEvent event) {
   // 两个 getDrawable 取得的对象, 需要用 mutate 设置一下
    Drawable drawable = getDrawable();
    if(drawable==null){
        return super.onTouchEvent(event);
    }
    switch (event.getAction()) {
        case MotionEvent.ACTION_DOWN:
            drawable.mutate().setColorFilter(Color.GRAY,  PorterDuff.Mode.MULTIPLY);
            break;

        case MotionEvent.ACTION_CANCEL:
        case MotionEvent.ACTION_UP:
            drawable.mutate().clearColorFilter();
            performClick();
            break;
    }
    return super.onTouchEvent(event);
}

// 补充 mutate 用法
如果多个控件使用同一个 Drawable ，
如果其中一个控件的 Drawable 发生改变，其他所有的Drawable都会发生改变。
如果使用Drawable.mutate()，就可以从 Drawable 里新建一个不可变的实例，
那么当这个Drawable发生改变时，不会导致其他的Drawable发生改变。
```

### 全屏、去掉标题栏
```text
//在setContentView之前设置
requestWindowFeature(Window.FEATURE_NO_TITLE); //Activity
supportRequestWindowFeature(Window.FEATURE_NO_TITLE); // ok  AppCompatDialog

getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);
getWindow().setFlags(WindowManager.LayoutParams.FLAG_FORCE_NOT_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);

//extends AppCompatActivity
android:theme="@style/Theme.AppCompat.Light.NoActionBar"

getSupportActionBar().hide();



```

### 启动默认浏览器
```text
Intent intent =newIntent();
intent.setAction("android.intent.action.VIEW");
Uri content_url =Uri.parse("http://www.xxx.com");
intent.setData(content_url);
startActivity(intent);
```

### 启动第三方应用
```text
PackageManager pm = this.getPackageManager();
Intent intent = pm.getLaunchIntentForPackage("xxx.包名.xxx");
try {
    startActivity(intent);
} catch (Exception e) {
    //请先安装
}
```

### 系统分享功能
```text
Intent wechatIntent = new Intent(Intent.ACTION_SEND);
wechatIntent.setPackage("com.tencent.mm");//微信
wechatIntent.setType("text/plain");
wechatIntent.putExtra(Intent.EXTRA_TEXT,  "hello world");
try{
    context. startActivity(wechatIntent);
}catch (Exception e){
    //请先安装微信
}
```


### 使用自定义字体
```text
AssetManager mgr = getAssets();
Typeface tf = Typeface.createFromAsset(mgr, "fonts/2.TTF");// 文件在assets/fonts目录下
textView.setTypeface(tf);
```


### XML解析
```text
SAX：Simple API for XML。 相比于DOM，SAX是一种速度更快，更有效的方法。它逐行扫描文档，一边扫描一边解析。 
不用实现调入整个文档，占用资源少。尤其在嵌入式环境中，如android，极力推荐使用SAX解析。
```

### 复制粘贴
```text
// 得到剪贴板管理器
ClipboardManager cmb = (ClipboardManager) context.getSystemService(Context.CLIPBOARD_SERVICE);
//复制
cmb.setText( msg);
// 粘贴
String msg=cmb.getText().toString()  
```

### 防止 Toast 重复提示
```text
private static Toast mToast;
public void showMsg(Context context, String msg) {
    if (mToast == null) {
        mToast = Toast.makeText(context, msg, Toast.LENGTH_SHORT);
    } else {
        //直接设置，不用重复创建
        mToast.setText(msg);
    }
    mToast.show();
}
```





### performClick
Custom view FloatingforView overrides onTouchEvent but not performClick less...
```text
case MotionEvent.ACTION_UP:
               performClick();
               break;

@Override
public boolean performClick() {
    return super.performClick();
}
```

### 保存控件截图
```text
public static Bitmap saveViewBitmap(View view) {
    view.setDrawingCacheEnabled(true);
    view.buildDrawingCache(true);
    Bitmap cacheBitmap = view.getDrawingCache(true);
    Bitmap destBitmap = Bitmap.createBitmap(cacheBitmap);
    cacheBitmap.recycle();
    view.setDrawingCacheEnabled(false);
    return destBitmap;
}
```

### 图片转byte[]
```text
ByteArrayOutputStream stream = new ByteArrayOutputStream();
bitmap.compress(Bitmap.CompressFormat.JPEG, 100, stream);
byte[] array = stream.toByteArray();
```

### 图片转Base64
```text
ByteArrayOutputStream bStream = new ByteArrayOutputStream();
bitmap.compress(Bitmap.CompressFormat.PNG, 100, bStream);
byte[] bytes = bStream.toByteArray();
return Base64.encodeToString(bytes, Base64.DEFAULT);
```


### 获取 Bitmap 缩略图
```text
public Bitmap getImageThumbnail(Bitmap bitmap0 ,  int width, int height) {
    Bitmap bitmap = ThumbnailUtils.extractThumbnail(bitmap0, width, height,  ThumbnailUtils.OPTIONS_RECYCLE_INPUT);
    return bitmap;
}
```


### 占位符
```text
<string name="string_test_1">学号：%1$d ;姓名：%2$s ;成绩：%3$.2f</string>
String result = String.format(testStr,1001,"张三",9.235);

%N代表第N个参数，如%3代表的是第三个参数；
$是结束符；
d/s/.2f代表的是整数/字符串/保留2位小数点的浮点数


xml 中配置占位符
 <string name="lowBatteryInfo">注意：当前电量低，只剩 %1$.4s %% ！请尽快返航！！！</string>
float percentV= 19.12342342342f;
String msg1 =getString(R.string.lowBatteryInfo) ;
String alertInfo  = String.format(Locale.getDefault() , msg1 , String.valueOf(percentV) ) ;
Log.i("aivin" ,"alertInfo="+alertInfo); // alertInfo=注意：当前电量低，只剩 19.1 % ！请尽快返航！！！
```

### 图片平铺
```text
<bitmap xmlns:android="http://schemas.android.com/apk/res/android"
    android:src="@drawable/redline_vertical"
    android:tileMode="repeat" />
android:background="@drawable/redline_verticalbitmap"
```

### 环境变量配置
```text
1、建->变量名：JAVA_HOME 变量值：D:/Java/jdk1.6.0_12(这只是我的JDK安装路径)
2、编辑->变量名：Path 在变量值的最前面加上：%JAVA_HOME%/bin;%JAVA_HOME%/jre/bin
3、新建->变量名：CLASSPATH 变量值：.;%JAVA_HOME%/lib;%JAVA_HOME%/lib/dt.jar;%JAVA_HOME%/lib/tools.jar
分别输入java，javac，java -version 命令测试是否配置成功
SDK解压，把解压后文件夹的tools目录添加到环境变量path中

查看配置好的变量、jdk当前路径、jdk安装路径
echO  %JAVA_HOME%  //字母o 

java javac 显示版本正常，也能够编译成功，运行java 的时候出现找不到类的错误。 在classpath中添加  .;  即可解决。

//JDK版本更改后，环境变量不生效
删除 C:\Windows\System32目录下 java.exe、javaw.exe、javaws.exe 三个文件。
删除Path中 C:\ProgramData\Oracle\Java\javapath 配置
```

### ByteBuffer相关
```text
// 深度克隆ByteBuffer
private  ByteBuffer deepCloneByteBuffer(ByteBuffer original) {
    ByteBuffer clone = ByteBuffer.allocate(original.capacity());
    original.rewind();
    // 用put接口，才会把byte数组的内容拷贝到ByteBuffer中
    clone.put(original);
    original.rewind();
    clone.flip();
    return clone;
}


//java.nio.ByteBuffer变量解释
int position 
读写位置，表示当前进行读写操作时的位置。
position的位置会随着数据的写入而移或者读取一个字节一个字节的移动。
比如添加一个byte类型的数据position会+1, 添加一个int类型的数据position会+4

int limit 
读写限制, 缓冲区可以进行读写的范围的最大值。

int capacity 
容量，它的值是在创建缓冲区的时候指定的，设定后无法对其进行修改。 

int mark 
临时标记变量，用来存储 postion的。


//常用方法
ByteBuffer= allocate(int capacity)  分配一个新的字节缓冲区。
ByteBuffer= allocateDirect(int capacity)  分配新的直接字节缓冲区。
int =position()  获取position
Buffer= position(10) ; 设置 position
int =limit() 获取limit
Buffer =limit(10) 设置limit
int =capacity() 获取capacity
byte[] = array()  
Buffer= flip() 把limit设为当前position，把position设为0
Buffer= mark() 将存储当前position的值，等下次调用reset()方法时，会设定position的值为之前的标记值；
Buffer= reset()  
Buffer= rewind() 把position设为0，limit不变
Buffer= clear() 把position设为0，把limit设为capacity。并不是清空内容

ByteBuffer= compact() 
将 position 与 limit之间的数据复制到buffer的开始位置，  
若 position 与limit 之间没有数据的话发，就不会进行复制 。 
其实就是删除已读过的数据复制后 position  = limit -position,limit = capacity  。
  
ByteBuffer=  wrap(byte[] array,  int offset,  int length) limit 等于 offset
ByteBuffer=  wrap(byte[] array ) 这个缓冲区的数据会存放在byte数组中，bytes数组或buff缓冲区任何一方中数据的改动都会影响另一方。
ByteBuffer= slice() 返回原ByteBuffer的一个镜像，所有改变互相可见
ByteBuffe=r put(byte[] src) 将一个数组写入
ByteBuffer= put(byte b) 将一个byte写入，postion+1
ByteBuffer= put(int index, byte b) 把src中可读的部分（也就是position到limit）写入此byteBuffer
ByteBuffer =get(byte[] dst) 将内容读取到然后放到 dst中
byte=  get() 读取一个byte
```





## 如何判断一个 APP 在前台还是后台
借用系统 ActivityLifecycleCallbacks 的回调来进行判断。
进入后台后，有一些机型貌似弹不出toast，比如华为M3 。
暂时没找到解决办法，我试过一些银行的APP 和所得税APP，同样也弹不出来。
```text
 // 原理：程序中任何一个activity处于后台时(onStope)都将触发提醒。 
 // 所以，如果顶层activity中onStope方法被执行，则认为程序处于后台。
public class MyAppLifecycleHandler implements Application.ActivityLifecycleCallbacks {
    private int started = 0;
    private int stopped = 0;
    private String lastActivitName ;
    private boolean flag = true;

    public MyAppLifecycleHandler(String name){
        lastActivitName= name ;
    }

    @Override
    public void onActivityCreated( ... ) {  }

    @Override
    public void onActivitySaveInstanceState( ... ) {  }

    @Override
    public void onActivityResumed(Activity activity) {  }

    @Override
    public void onActivityPaused(Activity activity) {  }

    @Override
    public void onActivityStarted(Activity activity) {
        ++started;
    }

    @Override
    public void onActivityStopped(final Activity activity) {
        ++stopped;
        if (!isApplicationInBackground()){
            return;
        }

        flag = true;
        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                if ( flag ) {
                    Toast.makeText(activity, "WkPlayer被切换至后台", Toast.LENGTH_SHORT).show();
                }
            }
            // 延时的作用是用来等待一下，看 onActivityDestroyed 是否被执行了。
        } ,500) ;
    }

    @Override
    public void onActivityDestroyed(Activity activity) {
        if (lastActivitName.contains(activity.getClass().getSimpleName())) {
            flag = false;
        }
    }

    private boolean isApplicationInBackground() {
        return started == stopped;
    }
}
// Application 中调用
MyAppLifecycleHandler lifecycleHandler = new MyAppLifecycleHandler( FileExplorerActivity.class.getSimpleName() ) ;
unregisterActivityLifecycleCallbacks(lifecycleHandler);
registerActivityLifecycleCallbacks(  lifecycleHandler ) ;
```










# 皮肤更换 、更换主题
## 更换皮肤核心原理
```text
重写 LayoutInflater.Factory 接口 View onCreateView(String name, Context context, AttributeSet attrs)  
自定义 xml中 view解析的过程，并将解析出来的属性和view保存起来
换肤的时候，遍历这些属性和控件，
重新设置此view的换肤属性对应的资源。

mSkinFactory = new MySkinFactory();
getLayoutInflater().setFactory(mSkinFactory);

如果是设置默认主题皮肤就是从当前的apk包中获取资源id
int originColor = context.getResources().getColor(resId);
如果是切换皮肤包，就从皮肤包中加载资源id
int newResId = mSkinResources.getIdentifier(resName, "color", skinPackageName);

注：皮肤包的本质就是一个apk，里面配置的资源名字跟主apk中的资源名字是一样的
```

## 暗黑模式省电原理
```text
目前主流的手机屏幕有 LCD (液晶) 、OLED (有机发光二极管) 屏幕。

LCD 屏幕背光始终被点亮，无论像素显示的是黑色还是其他颜色，都会消耗相同的能量。
早期手机屏幕基本上都是 LCD 。

OLED 屏幕像素点是单独工作的，当像素点是纯黑色时，该像素会被关闭，不消耗能量。
OLED 技术不需要背光的支持，省去了液晶和背光模组，结构非常简单，机身自然也可以达到极致超薄。

所以 只有 OLED屏幕在暗黑模式下省电。

android APP 可以自己设置暗黑主题皮肤包，
也可以简单配置一下，使用系统的暗黑主题。
Android 10 开始从系统层面支持暗黑主题。
```

## 换肤demo
```text
https://github.com/hnyer/XSkinLoader ,
在“史海钩沉”APP中有实际测试。
```

