# java常用代码

## java get 访问
```text
用来简单获取一下数据，测试一下服务器代码。
public static String get(String url) throws Exception {
    String content = null;
    URLConnection urlConnection = new URL(url).openConnection();
    HttpURLConnection connection = (HttpURLConnection) urlConnection;
    connection.setRequestMethod("GET");
    connection.connect();
    int responseCode = connection.getResponseCode();
    if (responseCode == HttpURLConnection.HTTP_OK) {
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader
                (connection.getInputStream(), StandardCharsets.UTF_8));
        StringBuilder bs = new StringBuilder();
        String l;
        while ((l = bufferedReader.readLine()) != null) {
            bs.append(l).append("\n");
        }
        content = bs.toString();
    }
    return content;
}
```


##  try-with-resources 写法
```text
使用try-with-resources优雅的关闭IO流
 try(FileInputStream input = new FileInputStream("file.txt")) {
      int data = input.read();
      while(data != -1){
          System.out.print((char) data);
          data = input.read();
      }
  }
注意方法中的第一行：这就是try-with-resource 结构的用法。
```
