# c语言 
## 数据类型

c基础数据类型|字节|说明
-|-|-
char|  1  |
unsigned char | 1|
signed char| 1 |
int | 2 或4  |
unsigned int|2 或4 |
short | 2 |
unsigned short|2|
long|  4 |   
unsigned long | 4 |
float |  4 |  
double |  8|  
long double |16|
没有 boolean  | | 0假 , 非0真
没有 byte  | | unsigned char

```text
signed int value;    // 有符号类型
int value;          // 不写就默认为有符号类型
unsigned int value;// 无符号类型
```

 
##  printf 输出格式 、占位符

类型type |对应类型 | 说明| 示例
-|-|-|-
d/i	| int	|输出十进制有符号32bits整数，i是老式写法|	printf("%i",123); <br>输出123
u|	unsigned int|	无符号10进制整数|	printf("%u",123);<br> 输出123
o|	unsigned int|	无符号8进制整数(不输出前缀0)	|printf("0%o",123);<br>输出0173
x/X	|unsigned int	|无符号16进制整数，x对应的小写，X对应的是大写（不输出前缀0x)|	printf("0x%x 0x%X",123,123);<br>输出0x7b 0x7B
f/lf|	float(double)|	单精度浮点数用f,双精度浮点数用lf(printf可混用，但scanf不能混用)|	printf("%.9f %.9lf",0.000000123,0.000000123);<br>输出0.000000123 0.000000123。<br>注意指定精度，否则printf默认精确到小数点后六位
F |	float(double)	|与f格式相同，只不过 infinity 和 nan 输出为大写形式。	|printf("%f %F %f %F\n",INFINITY,INFINITY,NAN,NAN);<br>输出结果为inf INF nan NAN
e/E|	float(double)|	科学计数法，<br>使用指数(Exponent)表示浮点数 <br>此处”e”的大小写代表在输出时“e”的大小写|	printf("%e %E",0.000000123,0.000000123);<br>输出1.230000e-07 1.230000E-07
g	|float(double)	|根据数值的长度，选择以最短的方式输出，%f或%e	|printf("%g %g",0.000000123,0.123);<br>输出1.23e-07 0.123
G|	float(double)	|根据数值的长度，选择以最短的方式输出，%f或%E	|printf("%G %G",0.000000123,0.123);<br>输出1.23E-07 0.123
c|	char	|字符型。<br>可以把输入的数字按照ASCII码相应转换为对应的字符	|printf("%c\n",64)输出A
s	|char*|	字符串。<br>输出字符串中的字符直至字符串中的空字符（字符串以空字符’\0‘结尾）|	printf("%s","测试test");<br>输出：测试test
S|	wchar_t*	|宽字符串。输出字符串中的字符直至字符串中的空字符（宽字符串以两个空字符’\0‘结尾）|	setlocale(LC_ALL,"zh_CN.UTF-8"); <br> wchar_t wtest[]=L"测试Test";  <br>printf("%S\n",wtest);  <br>输出：测试test
p	|void*|	以16进制形式输出指针	|printf("%010p","lvlv");<br>输出：0x004007e6
n	|int*	|什么也不输出。%n对应的参数是一个指向signed int的指针，在此之前输出的字符数将存储到指针所指的位置|	int num=0; <br> printf("lvlv%n",&num); <br> printf("num:%d",num); <br> 输出:lvlvnum:4
%	|字符%	|输出字符‘%’（百分号）本身	|printf("%%");输出:%
m	|无|	打印errno值对应的出错内容|	printf("%m\n");
a/A	|float(double)|	十六进制p计数法输出浮点数，a为小写，A为大写	|printf("%a %A",15.15,15.15);<br>输出：0x1.e4ccccccccccdp+3 0X1.E4CCCCCCCCCCDP+3

```text
//函数原型
printf("格式化字符串",输出表列)
//格式控制字符串
%[flags][width][.prec][length]type
%[标志][最小宽度][.精度][类型长度]类型

int value = 12345;
printf("右边补空格=%-10d\n", value);
printf("左边补空格=%10d\n", value);
printf("左边补零= %010d\n", value);

float value2 = 100.123456789;
printf("精度= %.5f\n", value2);
```

## 字符串
```text
//  '\0' 终止标记
char greeting[6] = { 'H', 'e', 'l', 'l', 'o', '\0' };
char greeting[] = "Hello";

//复制 str1 到 str3
strcpy(str3, str1);
//连接 str1 和 str2
strcat(str1, str2);
//字符串长度
len = strlen(str1);

char s1[] = "aivin666@qq.com";
char ch = '@';
// 返回首次出现目标字符的指针位置
char* location=strchr(s1, ch);
if (location)
{
  // qq.com
  printf("%s\n", ++location);
}
else {
  // 不存在就会返回NULL
}

char s1[] = "aivin666@qq.com";
char s2[] = "666";
// 返回首次出现目标字符串的指针位置
char* location= strstr(s1, s2);
if (location)
{
  //666@qq.com
  printf("%s\n", location);
}
else {
  printf("匹配失败\n");
}

// 字符串比较 ,相等表示 字符串内容相同
int memcmp(const void *buf1, const void *buf2, unsigned int count);
//匹配字符串，成功则返回第一次匹配到的地址，失败返回空
strcasestr(url, "rtsp://")
```

 

## 指针
指针是一个变量，存储了某个变量的内存地址。 void * 表示未确定类型的指针。
```text
int value = 200;
int* ip;      //  ip是一个指向int类型的指针
int *ip // 结果跟 int* ip 完全一样。（建议这样写）

ip = &value;// &符号是获得 value 这个变量的地址
int reslut = *ip;  // *是获得指针ip指向的变量的值

int* intPtr;
int value = 10;
intPtr  = &value; // &value 获得指向10的指针
*intPtr = 200;  // 直接将指针指向的那个值改成 200
```

### 二级指针 、多级指针
```text
int  **ptr2; //二级指针。 ptr2指向一个地址，这个地址中存放的变量是一个 int指针类型的数据。
int  ***ptr3; // 三级指针
```

### 数组指针
数组指针 指向数组的首地址
```text
int arr[] = {1 ,2,3,4};
int *ptr = arr; // 指向数组的首地址。数组跟基本类型不一样不需要用&取地址。arr可以认为就是一个指针了。
ptr++;
printf("%d\n" , *ptr);

int arr2[] = { 8 ,9 ,10 };
int (*ptr2)[3] = &arr2; // 定义一个指针。 数组指针 。
//int  *ptrArr[3];     //  定义一个数组。 指针数组。 
printf("bbb= %d\n",   (*ptr2)[0]   ); // *ptr2 获得数组，代表数组的首地址
int result =  * (*ptr2 +1 ) ; //对地址取值 
printf("ccc= %d\n", result); // 9
```

### 函数指针 、 方法指针
系统为函数代码分配一段存储空间，这段存储空间的首地址称为这个函数的地址。
函数名表示的就是这个地址。指向这个地址的指针叫做函数指针。   
函数指针可以像一般函数一样，用于调用函数、传递参数。
```text
// 定义
int sum(int x, int y)  {
	return x+y;
}

int main(void){
  // 函数返回值类型 (* 指针变量名) (函数参数类型列表) 
  int (*ptrTest) (int, int);  //定义函数指针,指针名字可以随意 
  ptrTest = &sum; // 将函数的地址给指针 ，可以这样理解，函数名就是一个指针

  int a = 20, b = 10, c;
  c = ptrTest(a, b);  //通过函数指针调用函数
  printf("a = %d\nb = %d\nmax = %d\n", a, b, c);
  getchar();
  return 0;
}

// 结构体中包含函数指针
struct SLPlayItf {
	SLresult  (*SetPlayState) ( SLPlayItf self, SLuint32 state );
};
(*pcmPlayerPlay)->SetPlayState(xx,  xx); //其他地方调用函数SetPlayState
```

## 函数回调
```text
int func3(int x ,int y)  
{
	return x + y;
}
// 第三个参数就是函数指针
int handle(int x, int y , int(*funcPtr)(int , int))
{
	return funcPtr(x , y);
}

void main(void)
{
	int x = 10, y = 20;
	int sum = handle(x, y, func3); // 函数名其实就是一个指针
	printf("sum= %d" , sum);
}
```

 

## 数组
```text
int arr[] = {1 ,2,3,4};

for ( int i : arr) {
  printf("%d  " , i);
}

int sum = sizeof(arr) / sizeof(arr[0]);
for (int i = 0; i < sum; i++) {
  printf("%d  ", arr[i]);
}
``` 

## 变量
注意c语言的全局变量和java的全局变量是不一样的。
```text
Java是一种面向对象语言，它不允许在类和接口之外声明函数。
C不是面向对象的，所以C中所有全局变量和函数本质上对Java而言都是静态的。

在C中，常会出现在一个文件中定义大量的全局变量，
在其他文件中通过引用声明这些变量来实现共享，但Java不允许有全局变量或函数；
```

## 堆栈 、内存
栈的内存最大值比较小，开辟方式用静态开辟。方法结束后会自动回收。 堆的内存最大值较大，用动态方式开辟。不会自动回收，必须通过手动释放才能被回收。

### 静态内存
```text
int arr[2*1024] ;// 静态开辟 2K内存
```

###  动态内存
```text
#include<malloc.h> 或者  <stdlib.h> 
//若成功就返回指向被分配内存空间的指针，否则就返回NULL
int *p = (int *)  malloc(sizeof(int) * 10); 

if (p == NULL)
{
  return ; 
}

memset(p, 0, sizeof(int) * 10); //初始化空间
// 初始化成功后默认初始化为0，无需手动初始化
int* reslut = (int*) calloc(10 , sizeof(int));
// 对申请的内存进行大小的调整
p = (int*)realloc(p, sizeof(int) * 10); // 将大小调整为10
free(p);// 手动释放内存
```

###  内存四区模型
```text
一个由c/C++编译的程序占用的内存分为以下几个部分
1、栈区：由编译器自动分配释放 ，存放函数的参数值，局部变量的值等。
2、堆区：由程序员分配释放， 若程序员不释放，程序结束时可能由系统回收 。
3、数据区：主要包括静态全局区和常量区。
   初始化的全局变量和静态变量在一块区域， 
   未初始化的全局变量和未初始化的静态变量在相邻的另一块区域。 
   程序结束后由系统释放
4、代码区：存放函数体的二进制代码。 
```
 

<br>

## 结构体
c的结构体与c++的类似，但是不如c++的结构体强大。
```text
// 普通方式
struct student {
  char name[20];     
  int age;          
} stu1, stu2;
struct student stu3;
stu3.age = 20;
printf("年纪=%d\n" ,stu3.age);

//带别名方式,STUDENT是别名
typedef struct student {
  char name[20];
  int age;
}STUDENT ;
// 别名方式 可以省略 struct
STUDENT stu3;
```

```text
// 空结构体
struct URLProtocol11111111111 ;
或
struct URLProtocol11111111111
```


## static
```text
1、C中的static可以修饰局部变量（java不行），
被static修饰的局部变量表示这个函数执行完后，这个变量不被释放，其值仍然保存着，
然后下次访问该函数时，这个变量的值即为上一次函数执行完后它的值。

2、C中如果用static修饰全局变量或函数，那么就表示该全局变量或函数不能被其他文件访问。

好奇怪。注意和Java中的static的不一样。
```

## 集合 fd_set
```text
fd_set readSet;  
FD_ZERO(&readSet);  //清空集合中所有的元素
FD_SET(serverSocket,&readSet);   //将serverSocket加入readSet集合
```

## 联合体 共用体
```text
所有成员变量共享一个内存,
对于Union的不同成员赋值，将会对其他成员重写，原来成员的值就不存在了。
其他的用法参考struct 。

union  Data {
  char name[20];     
  int age;          
} data1,  data2 ;
```


<br>

## 控制台 输入 输出
```text
printf(...) // 将指定的内容输出到控制台
scanf(...) // 接收键盘输入的内容,只有按下回车键scanf才会从缓冲区取走数据

int a ;
scanf("%d", &a );
printf("输入int是= %d\n", a );

char str[50] ;
scanf("%s", str );
printf("输入字符串是= %s\n", str );

char c ;
c = getchar();// 获得按回车符后的第一个字符。控制台程序测试常用来暂停屏幕
putchar(c);// 输出一个字符到 控制台

// 一个汉字占用两个字节
char c1, c2;
c1 = getchar();
c2 = getchar();
putchar(c1);
putchar(c2);
---------------------------------------------
char c1, c2;
//#include <conio.h>
// 输入完毕即被读取，无需按回车键 . 屏幕上会显示输入的内容
c1 = getche();
// 输入完毕即被读取，无需按回车键 。屏幕上不会显示输入的内容
c2 = getch();
printf("\nThe character getche()= %c \n", c1);
printf("The character getch() = %c \n", c2);
---------------------------------------------
char s[50];
// 输入字符串 （可以有空格）
gets_s(s);
// 输出字符串
puts(s);
---------------------------------------------
char c1;
c1 =getc(stdin);
putc(c1 ,stdout);
```

<br>

## 常用函数

### memcpy(...)
```text
// 内存拷贝
void *memcpy(void *dest, const void *src, size_t n)
```

### memset(...)
```text
// 内存重置
void *memset(void *s, int ch, size_t n);
将s中当前位置后面的n个字节 用 ch 替换并返回 s
```


## 文件操作
### 读写权限
[操作类型](http://www.cnblogs.com/kangjianwei101/p/5220021.html) | 特点 | 文件不存在时 |特点
-|-|-|-
r	|只读 |报错|读 任意位置
w	|只写 | 新建 |清空原文件，写入任意位置
a	|只写 |新建| 不清空 ，只能在尾部写
r+|读写 |报错 | 不清空， 任意位置读写
w+|读写 |新建	 |清空，任意位置读写
a+|读写 |新建 |不清空 ， 任意位置读 ， 尾部写


打开模式 | 只读 | 只写| 读写 |读写|读写|读写
-|-|-|-|-|-|-
文本模式|r| w|a| r+ | w+ | a+
二进制模式|rb|wb|ab| rb+ ,  r+b | wb+ , w+b|ab+  , a+b


```text
fseek(FILE *stream, long int offset, int whence)   // offset 偏移量 ,origin从哪里开始偏移。
ftell( ) //  得到文件位置指针当前位置相对于文件首的偏移字节数，可以用来表示文件大小。
rewind( ) // 指针重新指向一个文件的开头。
fread( ) // 将文件读取到指定数组中
fwrite( ) // 将数组中的内容写入文件
```
 

### 读文本（有中文）
```text
// 此程序读取ANSI编码格式正常，其他格式中文乱码
FILE * fp;
if ((fp = fopen("D:\\data1.txt", "r+")) == NULL)
{
  printf("can not open the file\n");
  exit(0);
}

char ch = fgetc(fp);
while (ch != EOF)
{
  putchar(ch);
  ch = fgetc(fp);
}
fclose(fp);
```
<br>

### 写文本（有中文）
```text
//此程序保存的编码格式是ANSI
FILE * fp;
if ((fp = fopen("D:\\data1.txt", "w+")) == NULL)
{
  printf("文件打开失败\n");
}
else {
  char str[] = "123 a b 中文测试 123 abc abcdefg";
  int len = strlen(str);
  for (int i = 0; i < len; i++)
  {
    fputc(str[i], fp);
  }
}
fclose(fp);
```

<br>

### 复制文件 ，二进制（图片，音频）
```text
char *read_path = "D:\\music1.mp3";
char *write_path = "D:\\music2.mp3";

FILE *read_fp = fopen(read_path, "rb");
FILE *write_fp = fopen(write_path, "wb");

//缓冲区域
int buff[50];
//每次读到的数据长度
int len = 0;
while ( (len = fread(buff, sizeof(int), 50, read_fp)) != 0)
{
  //将读到的内容写入新的文件
  fwrite(buff, sizeof(int), len, write_fp);
}
fclose(read_fp);
fclose(write_fp);
```

## socket
```text
AF_INET  //  IPv4
AF_INET6 // IPv6
SOCK_STREAM // TCP流
SOCK_DGRAM  // UDP数据报
SOCK_RAW  // 原始套接字
SOL_SOCKET //  被设置的选项的级别，套接字级别 。
SO_REUSEADDR   // TCP套接字端口复用
SO_RCVBUF 、SO_SNDBUF  // 设置缓冲区大小  
EINPROGRESS // 正在处理

inet_addr ()   //将字符串形式的IP地址 -> 网络字节顺序  的整型值
inet_ntoa ()   // 网络字节顺序的整型值 ->字符串形式的IP地址
htons ()   // 把整数转换成“网络字节序”

//tcp select  , 监听套接字，如果有数据到来就返回
int selectResult = select( serverSocket+1, &readSet, &writeSet, NULL, &tv);
FD_ISSET(serverSocket,&readSet)  //  检测serverSocket是否在readSet集合中

recv () //int recv（SOCKET s，char FAR *buf，int len，int flags）
第一个参数：接收端的套接字描述符，其实就是客户端对应的套接字
第二个参数：指向接受的数据所在的缓冲区
第三个参数：缓冲区的最大尺寸。 
第四个参数：一般写0即可。

pthread_rwlock_tryrdlock ()   //非阻塞读锁定　　
pthread_rwlock_unlock ()
```

## 其他
```text
#pragma pack (n)   //  C编译器将按照n个字节对齐。
#pragma pack ()  // 取消自定义字节对齐方式。
#pragma  pack (push,1)  // 把原来对齐方式设置压栈，并设新的对齐方式设置为一个字节对齐
#pragma pack(pop)  //  恢复对齐状态
```


## C开源库函数一般用 返回0代表执行成功的原因
```text
1、成功用0，失败返回-1 ,-2 ,-3 等错误码。
2、习惯写法，no news is good news , 成功了就不用处理，失败就要处理。
if( xx) {  // 处理失败... 因为0 为false ，就不用进来了}
```



# cpp 
C++是面向对象的语言。 是C的超集，兼容大部分C的语法结构 。

## 数据类型
基本数据类型 | 字节 | 说明
-|-|-
char|	1   |
unsigned char	 |1   |
signed  char |	1    |
int|	4    |
unsigned int|	4    |
signed int|	4    |
short int	|2    |
unsigned short int	|2    |
signed short int|	2    |
long int|	8    |
signed long int|	8    |
unsigned long int|	8    |
float|	4    |
double|	8    |
long double	|16    |
wchar_t	|2 或 4    |宽字符型


## 关键字
关键字 | 说明
-|-
auto| 自动存储变量的关键字，也就是申明一块临时的变量内存。auto不写则隐含自动变量 。auto int a 和 int a效果一样。或者 auto * result =xx ; 当xx类型暂时无法确定时，可以先表示为auto类型。让编译器根据表达式自动推断变量的类型。
decltype|
const|  定义有类型的常量
define |   定义没有类型的常量
cin | 键盘输入
cout|
cerr|
clog|
stringstream| 提供相关的转换和格式化
inline |为了提高函数的执行效率
operator| 运算符重载
friend | 友元

```text
explicit  // 修饰只有一个参数的类构造函数, 它的作用是表明该构造函数是显示的
implicit  // 修饰只有一个参数的类构造函数, 它的作用是表明该构造函数是隐式的
volatile  // 类型修饰符，表示可以被某些编译器未知的因素更改。
```


##  “箭头 -> ”  和“  点号 . ”操作符
```text
箭头（->）：左边必须为指针；
点号（.）：左边必须为实体。
```

## 前缀 、 后缀
```text
0x或0X表示十六进制，不带前缀则默认表示十进制。
整数常量也可以带一个后缀，后缀是U和L的组合，U表示unsigned，L表示long。
30ul       // 无符号长整数
```


## 预处理
所有的预处理器指令都是以井号（#）开头，只有空格字符可以出现在预处理指令之前。预处理指令不是 C++ 语句，所以它们不会以分号（;）结尾。 <br> 
预处理指令用于创建符号常量。该符号常量通常称为宏。
```text
#define PI 3.14159
#define MIN(a,b) (a<b ? a : b)   //带参数的宏

#define   KEY_WIFI
#ifdef    KEY_WIFI // 如果定义了KEY_WIFI标记 ，取值为100 否则取值 -20
#define   ARRAY_SIZE  100
#else
#define ARRAY_SIZE  -20
#endif

#ifndef KEY_WIFI // 如果没有定义过 xx ，就定义
#define KEY_WIFI
#endif
#if 1  // 条件编译
#define TYPE  "VITUS"
# else
# define TYPE "FE15"
#endif

cout << "大小=" << ARRAY_SIZE << endl;// 使用
cout << "类型=" << TYPE << endl;
```

## 常量
```text
可以用 define 、const定义常量
#define LENGTH 10  
const int  LENGTH = 10;
```


## 变量声明 、变量定义
声明不会分配存储空间，只有定义时才会分配存储空间。 可以使用关键字extern在任何地方（多次）声明一个变量。 但变量只能在某个文件、函数或代码块中被定义一次。
```text
#include <iostream>
extern int a;// 变量声明
int main ()
{
  int a, b; // 变量定义
  a = 10;// 实际初始化
  return 0;
}
```

### 变量初始化
```text
局部变量被定义时，系统不会对其初始化，必须手动赋值初始化。
定义全局变量时，系统会自动初始化它。
```

## 左值、右值 
```text
指向内存位置的表达式被称为左值表达式。
右值指的是存储在内存中某些地址的数值。
int g = 20;//ok
10 = 20; // wrong
```

## 异常
```text
try {
	throw "发生异常！";
}
catch  ( char * msg)
{
	cout <<  msg << endl;
}
```

## 友元函数 、友元类
```text
采用类的机制后实现了数据的隐藏与封装 。
但是，其他类的某个函数需要频繁地访问类的数据成员，
这时可以将这些函数定义为该函数的友元函数。
友元的作用是提高了程序的运行效率（即减少了类型检查和安全性检查等都需要时间开销），
但它破坏了类的封装性和隐藏性，使得非成员函数可以访问类的私有成员。
```

## 继承
```text
c++ 支持多继承
继承方式可选范围 public(常用)、protected 、 private(默认)  
class Rectangle: public Shape ,<继承方式2><基类名2>   
{
...
}
```

## 函数重载
```text
void print(int i) 
{...}
 
void print(double f) 
{...}
```

##  操作符重载 、 运算符重载

不能被重载的操作符 | 说明|其他
-|-|-
成员访问运算符    |    .
成员指针访问运算符 |   .*
域运算符    |   ::
长度运算符  |   sizeof    |变量的大小会根据编译器和所使用的电脑而有所不同。  
条件运算符  |    ?:

可以重载的操作符 | 说明
-|-
双目算术运算符	|+ (加)，-(减)，*(乘)，/(除)，% (取模)
关系运算符|	==(等于)，!= (不等于)，< (小于)，> (大于>，<=(小于等于)，>=(大于等于)
逻辑运算符|   &#124;&#124; (逻辑或)，&&(逻辑与)，!(逻辑非)
单目运算符| 	+ (正)，-(负)，*(指针)，&(取地址)
自增自减运算符 | 	++(自增)，--(自减)
位运算符| 	&#124; (按位或)，& (按位与)，~(按位取反)，^(按位异或),，<< (左移)，>>(右移)
赋值运算符|  	=, +=, -=, *=, /= , % = , &=, &#124;=, ^=, <<=, >>=
空间申请与释放| 	new, delete, new[ ] , delete[]
其他运算符| 	()(函数调用)，->(成员访问)，,(逗号)，[](下标)

```text
运算符重载是为了更方便地操作类类型数据而设计的。
重载的运算符参数中至少要有一个类类型或枚举类型.否则编译不过。
public:
	int whitePeople;
	int blackPeoople;

	void display()
	{ ...}


// 重载运算符 +
Group   operator+  (Group & g1 , Group &g2)
{
	Group sum;
	sum.whitePeople = g1.whitePeople + g2.whitePeople;
	sum.blackPeoople = g1.blackPeoople + g2.blackPeoople;
	return sum ;
}

Group g1  ,g2  , g3;
g1.whitePeople = 10;
g1.blackPeoople = 10;
g2.whitePeople = 100;
g2.blackPeoople = 200;
g3 = g1 + g2;
g3.display();
```



## 指针
```text
int *p ; int* p;  // p是指向一个整数类型的指针
x=*p// 把指针p指向的值赋值给x
p=&x// 把x的地址赋给p（指针） 。仅仅通过指针无法知道指向数组的大小
```

## 引用
```text
引用就是某一变量的一个别名，对引用的操作与对变量直接操作完全一样 。
对引用求地址，就是对目标变量求地址。
int a;
int &ra=a; //& 读作引用。

//此处函数的形参p1, p2都是引用
void swap(int &p1, int &p2)
{
  ...
}
```

### 指针与引用的区别
```text
1、不存在空引用。引用必须连接到一块合法的内存。
2、一旦引用被初始化为一个对象，就不能被指向到另一个对象。指针可以在任何时候指向到另一个对象。
3、引用必须在创建时被初始化。指针可以在任何时间被初始化。
```


<br>

## 输入输出
```text
int  a = 0;
// 输入数字
cout << "请输入一个数字 \n";
cin >> a;
cout <<   a << endl;
// 输入字符串(不能有空格)
cout << "请输入一个没有空格字符串 \n";
string mystr;
cin >> mystr;
cout <<   mystr << endl;
// 输入字符串(可以有空格)
string mystr2;
cout << "请输入一个有空格字符串 \n";
getline(cin, mystr2);
cout << mystr2 << endl;
```
<br>

## 类型转换
```text
string mystr;
float price = 0;
int quantity = 0;

getline(cin, mystr);
// 字符串转为 int
stringstream(mystr) >> quantity;
// 字符串转为 float
getline(cin, mystr);
stringstream(mystr) >> price;
cout <<   quantity  <<  " "<< price  <<endl;
```

```text
static_cast           //  类型强制转换，效果就相当于加个括号:int a=(long)b; 
reinterpret_cast      //   类型强制转换，与static_cast 有细微差别。
dynamic_pointer_cast  // 
dynamic_cast          //
```
 

##  传值参数  引用参数
```text
// 不会改变原始内容
int func1(int valuea, int value2)
{
	int result = valuea + value2;
	valuea += 10;
	return result;
}

// 会改变最原始的值的内容
int func2(int& valuea, int& value2)
{
	int result = valuea + value2;
	valuea += 10;
	return result;
}
```


## 模板
模板就是实现代码重用机制的一种工具。

### 函数模板
对函数模板的理解可以参考Java的泛型
```text
template<typename T>
int compare(const T& left, const T& right)
{
	return left+ right ;
}
int a = 10;
int b = 20;
float c = 10.6;
float d = 20.7;
cout << compare(a ,b ) << endl;
cout << compare(c, d) << endl;
```

### 类模板
```text
template < class或者也可以用typename T >
class类名｛
／／类定义．．．．．．
｝;
```

## 命名空间
命名空间用来解决 命名冲突的问题。本质上，命名空间就是定义了一个范围。
```text
namespace mSpaceA
{
	int func( int a  , int b )
	{
		return  a + b;
	}
}

namespace mSpaceB
{
	int func(int a, int b)
	{
		return a*b;
	}
}

int a = 10;
int b = 20;
cout << mSpaceA::func(a , b) << endl;
cout << mSpaceB::func(a, b)  << endl;
```


## 数组
```text
int mArray []  = { 5, 10, 15 };
// 长度
cout <<  end( mArray ) - begin(mArray) << endl;
```



## 字符串
```text
// string 风格的字符串
string str1 = "aaa";
string str2 = "bbb";
str1 += str2;
cout << str1 << endl;

// c 风格的字符串
char str3[] = "hello";
char str4[] = "world";
strcat(str3, str4);
cout << str3 << endl;
```

## 动态内存
```text
// 开辟空间
int* intArray = new int[5];
intArray[3] = 100;

for (int i=0; i< 5 ;i++)
{
  cout << intArray[i] << endl ;
}
//释放空间
delete intArray;
```

## 结构体
```text
// 结构体类型 和变量 分开定义
struct student {
 char name[20];    //姓名
 int age;          //年龄
};
struct student stu1, stu2;

// 结构体类型 和变量 同时定义
struct student {
 char name[20];
 int age;
} stu1, stu2;
struct student stu3;
```

## Union 联合体
```text
所有成员变量共享一个内存,
对于Union的不同成员赋值，将会对其他成员重写，原来成员的值就不存在了。
其他的用法参考struct 。
```

## enum 枚举
```text
//枚举值默认从0依次赋值
enum color
{
  red,
  green,
  blue
};
color paint = red;

// red为0 ， green为5 ， blue为6
enum color
{
  red,
  green = 5,
  blue 
} paint;
```



## 别名
```text
类型别名可以用来降低原始类型长度或容易混淆的类型名称。在C++中，任何有效的类型都可以有别名。
// WORD 是 int的别名
typedef unsigned int WORD;
```


## 类class 、对象
```text
C++中获得对象有两种方法
1、通过new获得
2、直接申明获得。
这种方法跟java的对象获得区别很大。java是不会分配空间的，此时调用会空指针。
而C++是声明对象并为其分配了空间的。 

new出来的对象类似于申请空间，因此需要delete销毁，而直接声明的对象则在使用完直接销毁。
new出来的对象需要使用指针接收，而直接声明的不用。
```

```text
c++ 可以直接调用外部文件的函数，为什么还要类和对象？ （Java只能调用static类型的外部文件函数）
答：c++ 虽然可以直接调用外部文件的函数，但是函数如果很多的话，不能直观地知道是属于哪个文件的，所以用类来进行来管理。
这也是c++比c更方便的地方。
```

```text
类的属性和方法 默认访问级别是private。
class Student{
	char *name;
	int age;
public:
	void showInfo() {
		cout << "名字="<< name << " 年龄" << age  << endl;
	}
	// 构造函数
	Student(char *name0, int age0){
		name = name0;
		age = age0;
	}
	// 普通函数
	void setAge(int age);
	void setName(char *name0);
};
// 在 类的外部定义函数
void Student::setAge(int age0){
	age = age0;
}

void Student::setName(char *name0){
	name = name0;
}

// 申明式获得一个对象。 用带参的构造函数申明了一个对象student (名字随意)。
Student student("Aivin"  , 21);  // 这个对象此时已经被分配了内存空间了。并不会有空指针 。注意与java的区别。
student.setAge(20);
student.setName("小明");
student.showInfo();

成员函数/ 构造函数后面跟 冒号 “:”表示的是赋值，这是c++的特性。
A( int aa, int bb ):a(aa),b(bb) { ...}  相当于  A( int aa, int bb ) { a=aa;b=bb;}
__func__ ：  一个预置在编译器中的宏，可以直接调用。是调用它的所在函数的函数名。
```

## STL
```text
C++ STL(标准模板库)是一套功能强大的模板类，提供了通用的模板类和函数，
这些模板类和函数可以实现多种流行和常用的算法和数据结构，如向量、链表、队列、栈。
```

## 格式化时间
```text
// time_t 只能精确到秒
time_t t = time(0);
char tmp[64];
strftime(tmp, sizeof(tmp), "%Y-%m-%d %H:%M:%S",localtime(&t) );
LOGI("当前时间是：%s" , tmp) ;
```
 
## 析构函数
```text
c++中函数前加 ~ 是表示此函数是析构函数。
用来释放程序运行中没有delete或者free的变量，完成扫尾工作，
一般的创建类如果不写析构函数会调用系统默认的析构函数，
如果用户有声明和定义，则调用用户的
/***构造函数*/
WlJavaCall(_JavaVM *javaVM, JNIEnv *env, jobject *jobj);

/***析构函数*/
~WlJavaCall();
```

## std::string 字符串
```text
std::string::npos // 常数, 等于size_type类型可以表示的最大值,用来表示一个不存在的位置。
std::string audio_codec_name("aaaaaa")  // 新建变量audio_codec_name，初始化为aaaaaa。
```

## std::pair 
```text
// 键值对
可以用过 first和second访问第一个和第二个元素
```

##  std::map
```text
insert( )  // 插入元素
find( ) // 根据key 查找，
end( ) // 指向map的末尾 , 表示没有找到
erase( ) // 删除一个元素
```

## 链表
```text
std::list  
push_back( )  //在末尾插入元素
sort( ) // 以自定义规则排序
begin()  //返回指向容器最开始位置数据的指针
c.end() // 返回指向容器最后一个数据单元+1的指针
clear( )  // 移除所有元素，容器清空。
```  

## c++队列 std::deque
一种先进先出(FIFO)的数据结构。

### 队列使用
```text
#include "queue"
std::queue<T> queue; // 创建队列 ，不需要alloc之类操作
queue.push(t);//入队
AVFrame *avFrame1 = queueFrame.front();// 返回队列中第一个元素。不会删除。
queueFrame.pop();// 删除第一个元素。没有返回
AVFrame *avFrame2=queueFrame.back();// 返回队列中最后一个元素
push_back( ) //  在末尾位置插入元素
at()  // 返回索引为pos的位置的元素 
```

## 置空操作 (void)
```text
SLresult result;
(void) result; // 这样的写法是将result结果置空？
```

## 多线程
```text
#include<thread>
void func(int a, string msg)
{
	this_thread::sleep_for(chrono::seconds(2));// 线程休眠
	cout << a << " " << msg  << endl;
}

thread::id mainThreadId = this_thread::get_id();
cout << "主线程Id=" << mainThreadId << endl;
cout << "CPU数量= " << thread::hardware_concurrency() << endl;


thread t1(func,  2, "函数参数");            //创建线程对象t1,绑定线程函数为func
cout << "子线程Id=" << t1.get_id() << endl;//输出t1的线程ID
//t1.join();  等待 t1线程执行完毕再往下面执行
t1.detach();不用管t1是否执行完 即可往下执行

//以lambda表达式作为被帮顶的线程函数
thread t4( [](int a, string msg)
{
  this_thread::sleep_for(chrono::seconds(3));
  cout << a << " " << msg  << endl;
}, 4, "lambda表达式形式创建子线程");
t4.join();

getchar();
```


## 文件操作
### 权限说明
文件打开模式| 说明
-|-
ios::in          |只读
ios::out        |只写
ios::app         |从文件末尾开始写，防止丢失文件中原来就有的内容
ios::binary     |二进制模式
ios::nocreate    |打开一个文件时，如果文件不存在，不创建文件
ios::noreplace  |打开一个文件时，如果文件不存在，创建该文件
ios::trunc  |打开一个文件，然后清空内容
ios::ate  | 打开一个文件时，将位置移动到文件尾


### 读取 txt
```text
// #include <windows.h>
// 解决中文乱码
string UTF8ToGB(const char* str)
{
	string result;
	WCHAR *strSrc;
	LPSTR szRes;

	//获得临时变量的大小
	int i = MultiByteToWideChar(CP_UTF8, 0, str, -1, NULL, 0);
	strSrc = new WCHAR[i + 1];
	MultiByteToWideChar(CP_UTF8, 0, str, -1, strSrc, i);

	//获得临时变量的大小
	i = WideCharToMultiByte(CP_ACP, 0, strSrc, -1, NULL, 0, NULL, NULL);
	szRes = new CHAR[i + 1];
	WideCharToMultiByte(CP_ACP, 0, strSrc, -1, szRes, i, NULL, NULL);

	result = szRes;
	delete[]strSrc;
	delete[]szRes;

	return result;
}

string path = "D:\\hello.txt";
ifstream infile;
infile.open(path , ios::in);

char mBuffer[100];
string msg;

if (infile.is_open())
{
  while (!infile.eof())
  {
    // buffer 大小
    infile.getline(mBuffer, 200);
    msg = UTF8ToGB(mBuffer);
    cout << msg << endl;
  }
}

infile.close();
```


###  写入 txt
```text
string path = "D:\\hello2.txt";
ofstream outfile;
// 追加模式  ios::app , 覆盖模式   ios::out
outfile.open(path, ios::app);

if (!outfile.is_open())
{
  cout << "文件创建失败" << endl;
  return 0;
}

for(int i=0 ;i<5 ;i++)
{
  outfile << "当前行数：" ;
  outfile << i;
  // 换行
  outfile << "\n";

}
outfile.close();
```


###   二进制文件 复制
```text
/** 目标文件路径 ， 原文件路径*/
bool copy_binary_file(const char * szDestFile, const char * szOrigFile)
{
	bool bRet = true;
	std::ofstream fout(szDestFile, std::ios::binary | std::ios::app);
	std::ifstream fin(szOrigFile, std::ios::binary);
	if (fin.bad())
	{
		bRet = false;
	}
	else
	{
		while (!fin.eof())
		{
			char szBuf[256] = { 0 };

			fin.read(szBuf, sizeof(char) * 256);

			if (fout.bad())
			{
				bRet = false;
				break;
			}
			fout.write(szBuf, sizeof(char) * 256);
		}
	}

	fin.close();
	fout.close();

	return bRet;
}

// 测试
string originPath = "D:\\music.mp3";
string destPath = "D:\\music2.mp3";
bool reslut = copy_binary_file( destPath.c_str() , originPath.c_str());
```


## 混合编程
C编译器 和 C++编译器对编译函数符号的生成规则是不一样的。所以不能直接互相调用，必须要显示地申明。

### c++ 文件中使用 c代码
```text
//__cplusplus是cpp中的自定义宏， 若果定义了这个宏 则表示这是一段cpp代码。
//cfun.h
#ifdef __cplusplus  
extern "C" {
#endif
 void cfun();
#ifdef __cplusplus  
}
#endif  

//cfun.c
#include "cfun.h"  
#include <stdio.h>  
void cfun()
{
	printf("hello world...我是c写的...\n");
}

// main.cpp
#include <iostream>  
#include  "cfun.h"
using namespace std;
int main()
{
	cout <<"我在cpp文件 \n"  << endl;
	cfun();
	return 0;
}
```
 

### c 文件中使用 c++ 代码
```text
// cppfun.h
#ifdef __cplusplus  
extern "C" {
#endif
void cppfunc( );
#ifdef __cplusplus  
}
#endif

//cppfun.cpp
#include "cppfun.h"
#include <iostream>  
using namespace std;
void cppfunc( )
{
	cout << "我是被cpp 打印的\n" << endl;
}

// main.c
#include <stdio.h>
#include "cppfun.h"

int main()
{
	printf("我在c文件中发起调用\n");
	cppfunc();
	return 0;
}
```

## 网络访问
一般用[curl](https://github.com/curl/curl) ？？


## 其他
```text
printf( )  // 输出到屏幕，用来做打印信息
sprintf()  // 输出到指定的字符串或数组中，用来格式化字符串。
char* config = new char[strlen(filter_config) + 1];
sprintf(config, "%s%c", filter_config, 0);
```