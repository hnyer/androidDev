# Java枚举
```java
[JDK1.5 ,+∞ ) 支持枚举

为了唯一性，所以规定了构造函数不能为public、protected
只能是private 或 不写
```

<br>
#### 简单枚举
```java
public enum CarType {
	BWO ,DZ ,AD
}


CarType type= CarType.DZ ;
switch (type) {
case BWO:
  System.out.println("宝马");
  break;
case DZ:
  System.out.println("大众");
  break;
case AD:
  System.out.println("奥迪");
  break;
default:
  break;
}


```


<br>
#### 枚举类
```java
public enum CarType {
	//初始化枚举实例 ，必须在构造函数前面
	BWOINFO(1 ,"宝马" ,500.0),
	// 初始的值 对应构造函数的顺序
	DZINFO( 2 ,"大众" ,600.0),
	ADOINFO(3 ,"奥迪" ,700.0) ;


	private int  carId ;
	private String carName ;
	private double price ;

	private CarType(int id ,String name ,double price){
		this.carId =id ;
		this.carName = name ;
		this.price = price ;
	}
  ...
  get set 方法
  ...
}



// 单个实例
CarType car = CarType.BWOINFO;
System.out.println(car.getCarName() + "  " + car.getPrice());
// 修改枚举实例的值
car.setPrice(888.0);


// 遍历枚举类中的所有实例
for (CarType temp : CarType.values()) {
  System.out.println(temp.getCarName() + "  " + temp.getPrice());
}
```

<br>
#### 枚举的缺点
```xml
每一个枚举值都是一个对象,在使用它时会增加额外的内存消耗 ，同时也会增加 DEX文件的大小 。
所以android官方文档建议尽量避免使用枚举。
```


<br>
#### Android 中枚举的替代方案 @IntDef
```java
1、添加依赖
implementation 'com.android.support:support-annotations:28.0.0-rc02'

2、定义
public class MyDefBean {
    //先定义 常量
    public static final int SUNDAY = 0;
    public static final int MONDAY = 1;
    public static final int TUESDAY = 2;
    //用 @IntDef "包住" 常量 声明构造器
    @IntDef({SUNDAY, MONDAY,TUESDAY })
    // @Retention 定义策略
    @Retention(RetentionPolicy.SOURCE)
    // 定义‘接口’
    public @interface WeekDays {}
}
3、使用
@MyDefBean.WeekDays
int currentDay = SUNDAY;
void test(@MyDefBean.WeekDays int today) {
		switch (today) {
				case MyDefBean.MONDAY:
						break;
				case SUNDAY:
						break;
				case MyDefBean.TUESDAY:
						break;
				default:
						break;
		}
}

```
