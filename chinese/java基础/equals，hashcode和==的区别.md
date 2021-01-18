# 相等判断
```text
针对基本数据类型
== 比较的是它们的值是否相同。

针对 类、接口、数组 这样的引用数据类型 。
== 比较的是它们在内存 (在栈中) 的地址。
如果要比较引用对象的值是否相同，就需要重写 equals() 和 hashCode() ,
根据自己定义的规则判断对象是否相同。
```

#  HashSet 是如何高效保证 元素不重复 
```text
java规定 Set中元素无序，元素不可重复。
如何高效地判断新加的元素是否与集合中的元素不一样，从而保证保证元素不重复 ？

我们可以用 equals() 依次判断是否相等。
但是，每增加一个元素就检查一次，那么当元素很多时，
后添加到集合中的元素比较的次数就非常多了。
这显然会大大降低效率。

所以 Java采用了哈希表的原理，
当集合要添加新的元素时，先调用这个元素的 hashCode() ,定位到在hash表中的位置。
如果这个位置上没有元素，它就可以直接存储在这个位置上，不用再进行任何比较了；
如果这个位置上已经有元素了，但是有可能是 发生了 hash 碰撞 ，
所以还需调用它的 equals() 与新元素进行比较，
相同的话就不存，不相同就再次求 hashcode 散列其它的地址。
这样一来实际调用equals方法的次数就大大降低了，几乎只需要一两次。
```


#  equals ()  和 hashcode () ， 为何必须一起重写
```text
Object.java中有一句注释：equal objects must have equal hash codes

如果重写了 equals() ，但是不重写 hashCode() ,
可能会出现 equals() 相等 ，但是 hashCode() 不相等 。
因为 equals() 的比较规则是你自定义的， hashCode() 规则还是系统默认，
肯定无法满足 equal objects must have equal hash codes 。

补充：其实如果只是简单地比较一下对象是否相等，我们不重写 hashCode 也没关系，
但是因为你不能保证 其他人用 HashMap 、HashMap 来使用你的对象，
因为 hashCode 不一样，即使 equals 相同，也被 HashXX 认为是两个不一样的对象。
 
在使用时可能会出现问题，例如：
如果两个 key  A 对象 和B对象， 它们 equal 相同 但是 hashCode 不一样，
你用 A作为Key 添加了对象 ，但是用B去查 ，结果会为空。
```
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801181512_osChina_重写hashCode.png)


# 如何重写 hashCode()
```text
@Override
public int hashCode(){
  int hashCode = 0;
  //自定义系数，随意写。只要不是特别大或特别小导致最后hash值查出int的范围即可
  int k1=5 ;
  //最后的hash值生成原则：用到类里面的所有属性。
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



 
