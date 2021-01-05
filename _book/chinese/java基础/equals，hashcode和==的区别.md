# 相等判断
```xml
针对基本数据类型
== 比较的是它们的值是否相同 。

针对(类、接口、数组)这样的引用数据类型 。
== 比较的是它们在内存中的地址（在栈中）。
如果要比较引用对象的值是否相同，就需要重写equals方法。可以根据自己定义的规则判断对象是否相同。
```

## hashCode()在hashSet这种集合中的使用
```xml
java规定： Set中元素无序，元素不可重复。
需要解决的问题：如何高效地判断新加的元素是否与集合中的元素不一样，从而保证保证元素不重复 ？

解决办法：
我们可以用equals方法依次判断是否相等。但是，每增加一个元素就检查一次，那么当元素很多时，后添加到集合中的元素比较的次数就非常多了。这显然会大大降低效率。

于是，Java采用了哈希表的原理：
当集合要添加新的元素时， 先调用这个元素的hashCode方法，就一下子能定位到在hash表中的位置。
如果这个位置上没有元素，它就可以直接存储在这个位置上，不用再进行任何比较了；
如果这个位置上已经有元素了（hash碰撞现象），就调用它的equals方法与新元素进行比较，相同的话就不存，不相同就（再次求hashcode）散列其它的地址。
这样一来实际调用equals方法的次数就大大降低了，几乎只需要一两次。
```


## 如何重写hashCode()
hashSet这种集合判断集合中对象是否相同，为了提升效率 ，首先进行hashCode比较 ，然后再调用 equals比较 。
```java
@Override
public int hashCode(){
  int hashCode = 0;
  //自定义系数，随意写。只要不是特别大或特别小  导致最后hash值查出int的范围即可
  int k1=5 ;
  //最后的hash值生成原则： 用到类里面的所有属性。
  hashCode = k1 * age +  name.hashCode();
  return hashCode ;
}

@Override
public boolean equals(Object obj){
  //对象地址完全一样，对象肯定相等
  if (obj == this){
    return true;
  }
  //如果类型都不一样了，肯定不相等
  if (!(obj instanceof Bean1))  {
    return false;
  }
  //根据自定义规则判断是否相等
  Bean1 bean = (Bean1)obj;  
    return bean.name.equals(name) && bean.age == age;  
}

```

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801181512_osChina_重写hashCode.png)



## 开发中需要注意的问题
```xml
重写了equals() 就必须重写 hashCode()方法。
同一个对象，hashCode 一定相同。

否则就会违背以下的规则：
equal objects must have equal hash codes
```

















·
