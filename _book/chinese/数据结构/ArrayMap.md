ArrayMap是Android api提供的 .

Android里的Bundle内部就是ArrayMap 。

ArrayMap并不适用于数据量比较大的场景，它是一种以时间换空间的优化。

通常比HashMap要慢，因为在查找时需要进行二分查找，增加或删除时，需要在数组中插入或者删除键。而这种操作比较耗时。



ArrayMap有两个数组 。第一个数组存放存放item的hash值，第二数组是把key，value连续的存放在数组里，通过先算hash在第一个数组里找到它的hash index，根据这个index在去第二个数组里找到这个key-value。

![img](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801171626_osChina_arrayMap.png)
