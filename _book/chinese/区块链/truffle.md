# truffle
```xml
https://github.com/trufflesuite/truffle
Truffle基于Javascript,
针对Solidity语言的一套开发框架。
为以太坊提供了开发环境、测试框架和资产管道，
旨在使以太坊开发更容易。
```

<br>
#### 安装
```xml
基于Windows
1、 需要先安装 nodejs
2、 安装
    npm install -g truffle
3、测试是否安装成功
    truffle version
```

<br>
#### 搭建工程
```xml
1、truffle init
初始化 truffle 项目。

contracts  //智能合约目录
migrations //发布脚本目录
test       //测试文件
truffle.js // ?
truffle.js //Truffle的配置文件

2、配置需要编译的.sol文件
编辑 migrations/1_initial_migration.js
// 获得指定的文件
var Migrations = artifacts.require("./Migrations.sol");
module.exports = function(deployer) {
  // 部署到区块链上
  deployer.deploy(Migrations);
};

3、编译合约  
 truffle.cmd  complile   // 所有的.sol都被编译了

4、配置部署地址信息
   编辑 truffle.js

   module.exports={
     networks:{
       development:{
         host:"localhost",
         port:8545,
         network_id:"*"//匹配任何netword id
       }
     }
   }

5、合约部署
truffle.cmd migrate

6、执行
 Migrations .deployed().then(instance => contract = instance)

```



truffle.cmd test
