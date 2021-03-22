### 1、红黑树结构

红黑树是一种近似平衡的二叉查找树，它能够确保任何一个节点的左右子树的高度差不会超过二者中较低那个的一陪。具体来说，红黑树是满足如下条件的二叉查找树（binary search tree）：

每个节点要么是红色，要么是黑色。

根节点必须是黑色

红色节点不能连续（也即是，红色节点的孩子和父亲都不能是红色）。

对于每个节点，从该点至null（树尾端）的任何路径，都含有相同个数的黑色节点。

在树的结构发生改变时（插入或者删除操作），往往会破坏上述条件3或条件4，需要通过调整使得查找树重新满足红黑树的条件。

在项目中 很多底层都是基于rbTree 性能效率非常高，例如treemap 就是有序的map底层就是红黑树结构

### 2、Hash键值对结构

在一般的数组中,元素在数组中的下标位置是随机的,元素的取值和元素的位置之间存在不确定的关系;

因此数组在查找值时,需要把查找值和一系列的元素进行比较;此时的查询效率依赖于查找过程中所进行的比较次数;

如果元素的值(value)和数组中的下标(index)有一个确定的对应关系(hash),

公式:index = hash(value);

这样的数组就称之为哈希表,哈希表最大的用处就是提供查找数据的效率;

一般情况不会把哈希码(hashCode)作为数组元素的下标,因为哈希码较大,容易越界;可以在哈希码和下标之间做映射关系,

数组会记录元素的添加顺序,并且允许元素重复;

哈希表不会记录元素的添加顺序(哈希算法进行排序使之一一对应),不允许重复,原因是:如果元素重复,导致哈希码值相等,导致下标相等

Hash结构的有点就是查询单挑数据结构快，`就是基于hash结构，所以读取数据块



### 3、Map介绍与底层

Map是所有双列集合的接口，所有以键值对方式存储的类或接口都实现或继承了Map接口。Map的实现类有HashMap，Hashtable，TreeMap等,ConcurrentMap接口也继承了Map接口,Map的存储方式是以 key 和 value的方式，也就是键值对。Map中的key是不能重复的value是可以重复的。Map的底层是使用数组加链表的方式实现的。数组中的每一项就是一个单项的链表。存储键值对时通过key的hash值获得在数组中的位置。在通过key的equals获得在该数组位置上的链表的位置。通过key定位value。Map在每一次增加一个键值对时都会遍历整个集合，找出是否有和新增的键值对相同的key。如果有就修改这个key上的value如果没有就将新的键值对加入到集合中。通过这种方式保证key的唯一性

 

 

### 4、HashMap 与Hashtable与TreeMap的区别

HashMap 和Hashtable 都是Map的实现类。方法的使用和底层结构基本相同都是数组加链表。不同的是HashMap是Map的非同步实现 允许Map中出现null的键值。HashMap中的方法是没有同步锁的，如果在多线程的环境下使用需要提供外部同步才能保证数据的不混乱。Hashtable是Map的同步实现不允许键值为null。Hashtable的key或者value 一旦为null 会出现空指针异常。Hashtable 的方法中都拥有synchronized修饰，当一个线程对Map进行操作时会锁住整个哈希表。保证了Hashtable 在多线程的环境使用不会数据混乱。HashMap的操作效率要高于HahsTable一些。（例如向HashMap中添加10000个元素需要15毫秒而用Hashtable需要32毫秒）

TreeMap是一个按照键排序的Map，数据装入TreeMap后，会自动按照键的大小进行排序，输出的时候也是有序的，单同时线程也是不安全的，所以需要按照键大小排序的时候，可以使用TreeMap

 

### 5、怎么使hashMap变的线程安全concurrentHashMap是什么实现原理

HashMap的方法上是没有同步锁的，所以在多线程环境下使用是不安全的。我们可以在使用HashMap操作数据的方法或代码块上添加上同步锁synchronized，使多线程环境下线程排队操作。但是这样会造成效率的下降，因为每次操作数据整个集合都会被锁定。Hashtable类的方法中本身就有synchronized线程锁。同样Hashtable的效率也比较低。而ConcurrentMap中使用了分段锁的方式给集合中的部分数据锁定。它将集合中的数据分成多段，每段分别上锁。也就是说当不同的线程操作不同的数据段时不受锁的使操作影响，效率上高出了Hashtable很多。 

（分段锁的原理:ConcurrentHashMap是由Segment数组结构和HashEntry数组结构组成。Segment是一种可重入锁ReentrantLock，在ConcurrentHashMap里扮演锁的角色，HashEntry则用于存储键值对数据。一个ConcurrentHashMap里包含一个Segment数组，Segment的结构和HashMap类似，是一种数组和链表结构， 一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素， 每个Segment守护者一个HashEntry数组里的元素,当对HashEntry数组的数据进行修改时，必须首先获得它对应的Segment锁。）

### 6、HashMap和ConcurrentHashMap区别

从JDK1.2起，就有了HashMap，HashMap不是线程安全的，因此多线程操作时需要格外小心。

在JDK1.5中，Doug Lea给我们带来了concurrent包，从此Map也有安全的了。

ConcurrentHashMap具体是怎么实现线程安全的呢，肯定不可能是每个方法加synchronized，那样就变成了HashTable。

从ConcurrentHashMap代码中可以看出，它引入了一个“分段锁”的概念，具体可以理解为把一个大的Map拆分成N个小的HashTable，根据key.hashCode()来决定把key放到哪个HashTable中。

在ConcurrentHashMap中，就是把Map分成了N个Segment，put和get的时候，都是现根据key.hashCode()算出放到哪个Segment中：

ConcurrentHashMap和Hashtable主要区别就是围绕着锁的粒度以及如何锁,可以简单理解成把一个大的HashTable分解成多个，形成了锁分离。

ConcurrentHashMap（整个Hash表）,Segment（桶），HashEntry（节点）

ConcurrentHashMap的工作机制就是通过把整个Map分为N个Segment（类似HashTable），可以提供相同的线程安全，但是效率提升N倍，默认提升16倍。



### 7、HashMap源码

HashMap是双列的键值对结构，继承了AbstractMap类，实现了Map接口效率非常高

HashMap底层变量

transient Node<K,V>[] table; map底层是node数组，node实现于Map.Entry,由hash key vlaue next组成

static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16默认的初始化数据长度

static final int TREEIFY_THRESHOLD = 8; 默认的扩容树长度

static final float DEFAULT_LOAD_FACTOR = 0.75f;默认的负载因子，数组占用率超过0.75进行扩容

int threshold;//扩容长度 数组长度*负载因子

 当我们往HashMap中put元素的时候，判断数组是否为null如果为null调用resize()进行扩容初始化数组

在根据key的hashCode进行高位异或计算hash值，根据hash值&数组最大下标 得到这个元素在数组中的位置（即下标）， 高位异或是为了尽可能减少hash碰撞的情况

如果数组该下标上没有数据，则根据key value hash创建一个Node节点，放入数组下标中，如果该数组下标是有元素的，那么证明发生了hash碰撞就吧改下标元素拓展为链表结构，将新Node节点放在老Node节点中的next节点中。此时就完成了链表结构的拓展，但是如果新增节点的时候，hash碰撞较高的话会导致链表效率，也会影响性能，所以链表赋值的时候，进行了拓展如果链表超过8个长度的话，则将链表转为红黑树结构存储，从而提高查询效率

当数组占用率达到负载因子占用率的时候，数组长度双倍进行扩容，扩容之后，需要将老数组节点遍历重新根据hash&新数组最大下标进行计算下标，然后重新赋值计算node节点应该在的位置

从HashMap中get元素时，首先计算key的hashCode，找到数组中对应位置的某一元素，然后通过key的equals方法在对应位置的链表中找到需要的元素。

   归纳起来简单地说，HashMap 在底层将 key-value 当成一个整体进行处理，这个整体就是一个 Entry 对象。HashMap 底层采用一个 Entry[] 数组来保存所有的 key-value 对，当需要存储一个 Entry 对象时，会根据hash算法来决定其在数组中的存储位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Entry时，也会根据hash算法找到其在数组中的存储位置，再根据equals方法从该位置上的链表中取出该Entry。

###  8 ,什么是hash碰撞？

​     在put元素的时候，会将key的hash码经过计算& 上数组最大下标，不通的key计算出来的下标可能是一样的，这就是hash碰撞，这是一个正常现象，发送哈希碰撞后，会将新增的node节点放在老节点的链表中，

### 9， HashMap中put过程？

​     对key计算hash值，然后根据hash值计算出下标

根据下标看是否存在元素，如果没存在则直接放入该下标内如果该下标有值则发生hash碰撞，以链表的形式放在node的下一个节点

发生碰撞后如果新key和老key的是一样的，则走覆盖 ，hashmap不允许值重复

​     如果链表的长度超过规定阈值8则转为红黑树结构存储

如果数组占用率超过负载因子0.75则进行数组扩容 容量翻倍，然后便利每个节点重新根据hash计算下标存储到扩容后的数组中，

### 10.HashMap中的hash是如何计算的

高16位不变，低16位做了一个异或

（数组长度-1） & hash 计算下标