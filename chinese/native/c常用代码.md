# 通用代码
##  字符串转转大写
```text
void charsToUpper(char *s) {
    // 这种写法会报黄色警告
    //int len = strlen(s);
    int len = sprintf(s, "%s", s);
    for (int i = 0; i < len; i++) {
        if (s[i] >= 'a' && s[i] <= 'z') {
            s[i] -= 32;
        }
    }
}
// string 转char 指针
//std::string md5Result = md5.hexdigest();
//char *currentAppMd5 = const_cast<char *> (md5Result.c_str());
```