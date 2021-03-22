### 1、数组结构

数组是最简单的数据结构;是用来存放同一种数据类型的集合.

从增删改查分析数组的性能:

增:在数组的最后一个位置添加元素是很方便的,但是要是想在第一个位置添加元素就很麻烦了,后面的所有元素都要整体后移,容量不够还要进行扩容;

删:把数组的最后一个元素删除是很方便的,但是要删除第一个位置的元素就很麻烦,后面的所有元素都要整体前移;

改:修改指定下标的元素只要操作一次即可;

查:如果查询指定下标的元素只要操作一次即可,如果查询指定元素的下标,此时需要使用线性搜索(挨个找),

综上:数组的改查性能比较高,增删性能比较低;

### 2、List介绍与底层以及扩容机制

首先List 是集合类中的一个接口，继承了Collection接口，List 的实现类有ArrayList、LinkedList、Vector等。List集合中的元素是有序可重复的并且有下标的。ArrayList集合和Vector集合的底层使用Object类型是数组实现的。由于数组的长度是固定的所以当集合中的元素被装满时就需要扩容。ArrayList调用无参实例化后是空数组没有长度的，当调用增加方法时，会开辟10个长度,当ArrayList中的元素被装满时会以1.5倍的长度扩容，底层是以数组长度+数组长度位移>>1实现，所以是1.5倍，LinkedList底层node节点，记载了本身的值和上一个下一个的相邻元素的位置。linkedList 是一个双向链表，没有初始化大小，也没有扩容的机制，就是一直在前面或者后面新增就好。List集合是有下标的可以使用for循环通过下标遍历，也可以使用 迭代器 Iterator或forEach等不通过下标的方式遍历

### 3、ArrayList linkedList Vector区别

ArrayList和LinkedList 和Vector都实现了 List接口，所以他们中的一部分操作和方法是相同的。不同在于底层的数据结构 和操作效率上。ArrayList 底层使用的是 数组式的顺序存储 ，特点是空间连续，读取操作效率高  一旦进行元素的插入或移除操作可能会降低效率。LinkedList 底层使用的是链式/链表存储 空间不连续 通过指针指向下一个元素，读取操作效率低 ，进行元素的插入或移除操作效率较高。当我们对集合进行 较多的读取操作时 建议是 ArrayList 当我们对集合进行 较多的插入或移除操作时 建议是 LinkedList。最后是线程方面，Vector 中的所有方法都是是带有 线程锁的 synchronized，这样使它在多线程环境中使用不会造成数据的混乱，更适合多线程的环境使用。









### 4、ArrayList源码

List是个接口没有底层，所以直接讲解ArrayList底层

首先ArrayList继承于AbstractList抽象类，实现了，List，和序列化的接口，

ArrayList的底层变量有，

 

默认的数组长度  private static final int DEFAULT_CAPACITY = 10;

一个空数组  private static final Object[] EMPTY_ELEMENTDATA = {};

底层数组  transient Object[] elementData; 

集合长度  private int size;

ArrayList集合底层是一个Object类型的数组elementData，所以list可以放入任意类型的元素，

ArrayList无参构造中默认实例化了一个空的object数组给elementData赋值，所以初始长度为0，在add增加的元素方法中会有一个方法判断ensureCapacityInternal，如果elementData是空数组的话那么会自动增加一个元素长度为10的数组，装入数据后赋值给elementData，默认长度是10 是由DEFAULT_CAPACITY这个变量规定，

ArrayList有参构造中会传入一个集合长度initialCapacity，这是会判断如果initialCapacity大于0则创建这个长度的Object数组赋值给elementData，如果initialCapacity 等于0则用默认的空数组赋值给elementData，如果小于0则会抛出异常Illegal Capacity:

ArrayList中的get方法，获取数组会传入下标，首先有个rangeCheck方法判断当前下标是否大于数组长度，如果大于报异常，如果小于则返回elementData【下标】

ArrayList中的add方法，会先判断是否是elementData是否是空数组，如果不是，则会elementData【size++】=值，如果数组长度不够用则会进行当前数组长度1.5倍扩容

新长度=老长度+老长度>>1 (右移1是除以2的意思)

int newCapacity = oldCapacity + (oldCapacity >> 1);

ArrayList的clear清除方法，就是一个for循环，终值是size，然后将elementData【i】设置成null

 

ArrayList  底层代码有很多，列举了一些，其他的不一一列举

步骤，vector底层与ArrayList底层实现基本一致，只不过在所有的方法上都有synchronized修饰

### 5、LinkedList源码

LinkedList底层是基于双向链表结构设计的，有一个node节点 节点有三个属性，一个是prev上级节点，一个是next下级节点，另一个是node本身item，当增加元素的时候，要创建一个node。如果是第一次创建，则将元素赋值在node中的本身item属性，prev next 设置为null， 但是会将 node 标记为first，和last属性，证明是链表中第一个元素也是最后一个元素，然后第二次增加 的时候，将第二次元素设置为item 将 取出最后一个元素last 赋值为第二次元素中的上级信息prev 在将第2次元素 赋值到last属性。这个时候，链表中有两个元素，第二个元素是最后一个属性了，然后在增加元素的操作就跟第二次增加一样了，不停的创建node节点，增加一个元素就将元素设置为last就可以