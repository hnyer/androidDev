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
