# Solidity
```xml
Solidity  https://github.com/ethereum/solidity
Solidity 是以太坊智能合约编程语言 ，是一种静态类型语言 。
在以太坊平台上，Solidity编写的智能合约可以被编译成字节码在以太坊虚拟机上运行。
```

#### Solidity IDE  remix
```xml
IDE开源 https://github.com/ethereum/remix-ide

//windows下安装步骤
1、安装 node 、nvm
2、通过npm 安装
   npm install remix-ide -g
3、启动
   remix-ide
4、使用（建议使用Chrome，不要用猎豹、360等）
   http://127.0.0.1:8080
```

<br>

```xml
Solidity类型分为两类：值类型 、 引用类型
```
值类型|说明|举例
-|-
布尔类型|bool|bool reslut = false
整型| uint8，uint16，uint24......uint256(uint默认的是uint256) <br> 步长8  | int8  , uint16
定长浮点型|fixed/ufixed: 代表是否有符号 <br>M 代表整数有效位数（M必须能够被8整数且范围 [8,256] ） <br>N 代表小数有效位数（N的范围 [0 ,80] <br>ufixed 和fixed 分别是 ufixed128x19 和fixed128x19的别名. <br>目前只能申明变量，还不能赋值|ufixed256x80 value <br>fixed8x2 valueFix
定长字节数组|bytes1, bytes2,   …, bytes32  <br> byte的别名就是 byte1|  bytes1 public b = 0x6c;
有理数和整型常量|整数常量和有理数常量从属于数字常量。 可以参考对应的数学概念 <br>|
字符串常量|string STR_KEY ="TOM" <br> string STR_KEY2 ='TOM2'|
十六进制常量|hex"001122ff"| 可以转为bytes类型 ， bytes mByts = hex"001122ff"
枚举|enum|
函数|function |
地址|地址的长度为20字节|
地址常量|| 0xca35b7d915458ef540ade6068dfe2f44e8fa733c








引用类型|说明|举例
-|-
不定长字节数组|bytes  动态大小字节数据|
字符串|string  动态大小utf-8编码的字符串 <br> 不提供长度查询和按序号的访问|
数组| 分为固定长度数组 和动态数组  |uint [] array  <br> uint [5] arrayFixed
结构体|struct|



<br>
#### 变量、函数的 可见性
```xml

函数声明 默认是public  ，
变量声明 默认是internal

// public
外部可以调用被public修饰的函数和变量。
不用给变量手动写get函数。

// internal
内部能够正常访问，子类能够正常继承，但是不能供外部访问


// private
跟java的private一样

// external
只能修饰函数，说明这个函数只能被外部合约调用。
假设函数f()是external，还想在合约内调用，可以用this.f()。
```

<br>
#### 变量存储
```xml
变量的存储位置属性有三种类型
1、memory
越过作用域即不可被访问的临时变量 。
函数参数、返回的参数 默认是memory 。

2、storage
数据将永远存在于区块链上 。
函数内局部变量默认是storage,
合约内的全局变量 是 storage .

3、calldata
不会被永久存储 ，只读 。   
```





<br>
#### 数组
```xml
// 固定长度数组
string [5] arrayFixed ;
// 动态数组 ： 长度不定（可以动态修改）
string [] array ;
array.push(100);
array[0] = 110 ;
array.length = 20; // 重新设置数组的长度
array[15] = 90 ;
return array[15] ;
```




<br>
#### 定长字节数组
```xml
// 可以存放八个字节
bytes8 b8 = 0x6c69797565636875 ;
// 定长字节数组的长度
b8.length ;
```

<br>
#### 不定长数组 和 字符串 string
```xml
string str ="abcdefg" ;
// 初始化 bytes的长度为2
bytes    nameBytes = new bytes(2);  
 function test() returns (uint len) {
     // 将 string 转为 bytes
     bytes myBytes =bytes(str) ;
     // bytes的长度
     myBytes.length ;
     // 获取bytes某个值
     myBytes[1] ;
     bytes1 b1 ;
     //修改bytes某个值
     myBytes[2] = b1;

     // 重新设置bytes的长度
     nameBytes.length = 100 ;
     // 往bytes中添加byte
     nameBytes.push('Z') ;
     return nameBytes.length ;
 }
```




<br>
#### 支持 乘方 操作
```xml
//5 的 2次方
5 ** 2 = 25
```


<br>
#### 结构体
```xml
struct Person {
    uint age ;
    string name ;
}
Person p1 = Person(172, "name1");
```




<br>
#### 枚举
```xml
enum  Fruit {
    Apple  , Banana , Orange
}

Fruit fruit  = Fruit.Apple ;
if(fruit == Fruit.Apple){...}
```


<br>
#### 构造函数 、析构函数
```xml
/*
构造函数不是必须的 。
构造函数就是方法名和合约名字相同的函数 。
合约初始化时会调用 。
只能有一个构造函数
*/
function Demo(uint number, uint p)  { ...}

// 销毁合约
function killMyDemo() {
  if (owner == msg.sender) { // 检查谁在调用
     selfdestruct(owner); // address owner;
  }
}

```

<br>
#### 函数
我们约定（不是硬性规定）
函数里的变量都是以(_)开头以区别全局变量，
私有函数的名字用(_)开头
```xml
// 修饰符 view ，指明函数只能读取数据不能更改数据
function getInfo() public view returns(string str){
    return "hello world" ;
}
// 修饰符 pure ,表明这个函数甚至都不访问应用里的数据
function getInfo() public pure returns(uint value){
    return  value **2;
}

// 函数可以有多个返回值
function getPeopleInfo() public returns (uint _age , string _name){
    return (20 , "tom" ) ;
}
uint pAge ;
string pName ;
function test(){
     // 接收全部返回值
     (pAge ,pName ) = getPeopleInfo();
     //接收部分返回值
      (  ,pName) = getPeopleInfo();
}
```



<br>
#### 散列函数  、 类型强制转换
散列函数 keccak256 把一个字符串转换为一个256位的16进制数字
```xml
bytes32    result = keccak256(_name);
uint rand = uint(result);  
```



<br>
#### 地址
```xml
// msg.sender  系统自带的全局变量 ，代表当前调用者（合约）的地址 。
// msg.value 系统全局变量 ，代表当前合约的余额
address myAddress  =  msg.sender ;
// myAddress.balance  地址的余额
if (msg.value > 100 && myAddress.balance < 10){
    // 往指定地址发送 10 wei
     myAddress.transfer(10);
     // 跟 transfer类似，但是更低级 ，推荐使用 transfer
     myAddress.send(10);
}
```


<br>
#### 映射
类似java中的map 。通过键值对的方式管理数据
```xml
申明键映射，
键的类型为 address ，值的类型为uint
mapping(address => string) public peopleoInfo;
// 保存键值对
peopleoInfo[msg.sender] =  "tom";
// 根据键取值
string name= peopleoInfo[msg.sender] ;

```



<br>
#### require 验证前置条件
```xml
// 如果满足条件，就会往下执行 ，
// 如果不满足就会抛出错误，并终止程序
require(keccak256(_name) == keccak256("tom"));

```

<br>
#### if 判断
```xml
跟java一样
```

<br>
#### 判断字符串相等
```xml
Solidity不支持原生的字符串比较,
我们只能通过比较 两字符串的 keccak256 哈希值来进行判断

if (keccak256(_name) == keccak256("tom")){...}  
```

<br>
#### Import 导入 合约
```xml
一个 .sol 文件可以写多个合约。
但是为了方便管理 ，我们通常将不同的合约写到不同的文件中。
然后用 import 导入使用 。
import "./Animal.sol"  ;
```

<br>
#### 继承
```xml
// 用 is 表示合约的继承关系
contract Dog  is Animal {
...
}
```

<br>
#### 接口
```xml
// 定义接口
注意没有{ } 。
contract InfoInterface {
    function getInfo( address _address) public view returns ( string _name ) ;
}

// 使用接口
？？
// 调用接口
？？
```


<br>
#### 事件
```xml
//
```

<br>
#### 与其他合约的交互
```xml
//
```






<br>
#### modifier 修饰符
```xml
```


<br>
#### 1
```xml
```

<br>
#### 1
```xml
```


<br>
#### 1
```xml
```

<br>
#### 1
```xml
```


<br>
#### 1
```xml
```
