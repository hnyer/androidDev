# UML类图
```xml
Unified Modeling Language
统一建模语言
```

### 绘制UML类图的软件推荐 Enterprise Architect 或 [processon](https://www.processon.com/)

作用域类型 |符号
-|-
public| +
private| -
protected| #

<br>

元素类型| 图示
-|-
包|![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201804110945_osChina_包.png)
类|![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201804110939_osChina_类.png)
接口|![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201804110939_osChina_接口.png)

<br>

元素之间的关系 | 定义 | 举例 |  备注
-|-|-|-
泛化（Generalization）|继承|![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201804111002_osChina_泛化.png)|箭头指向父类
实现（Realization）|类实现接口|![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201804110954_osChina_实现.png) |箭头指向接口
关联（Association)|拥有关系|![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201804111008_osChina_关联.png)|箭头指向被拥有者
聚合（Aggregation）|整体与部分的关系。<br>部分可以单独存在。|![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201804111151_osChina_聚合.png)|菱形指向整体
组合(Composition)|整体与部分的关系<br> 部分不能单独存在。|![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201804111035_osChina_组合.png)|图标有2种<br>菱形指向整体
依赖(Dependency)|A需要B的协助<br>才能完成某项功能|![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201804111044_osChina_依赖.png)|箭头指向被使用者
