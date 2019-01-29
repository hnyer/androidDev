Hashtable和HashMap的内部数据结构相似.

![imgs](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801171540_osChina_哈希表_hashmap结构.png)

 HashMap由数组+链表组成的，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的。

 HashMap中的链表出现越少，性能才会越好。

 ```html
加载因子(DEFAULT_LOAD_FACTOR)越大，对空间的利用更充分，后果是查找效率的降低；
加载因子太小，那么散列表的数据将过于稀疏，对空间造成严重浪费。
默认加载因子为0.75，一般情况下我们是无需修改。

当哈希表中的条目数超出了加载因子与当前容量的乘积时，则要对该哈希表进行重建内部数据结构.
 ```


##### HashMap存储的步骤put（K,V）
传入key和value，计算key的hash值，根据hash值搜索在哈希表table中的索引位置，若当前索引位置不为null，则对该位置的Entry链表进行遍历，如果链中存在该key，则用传入的value覆盖掉旧的value，同时把旧的value返回。
否则调用addEntry，用key-value创建一个新的节点，并把该节点插入到该索引对应的链表的头部

##### HashMap的读取实现get（key，value）
 读取的步骤比较简单，调用hash（key）求得key的hash值，然后调用indexFor（hash）求得hash值对应的table的索引位置，然后遍历索引位置的链表，如果存在key，则把key对应的Entry返回，否则返回null

##### HashMap键的遍历，keySet()
 HashMap遍历时，按哈希表的每一个索引的链表从上往下遍历，
