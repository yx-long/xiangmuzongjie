### 1、String介绍与底层 

string字符串类型，这个类是被final修饰的类，被final修饰的类不能被继承，所以没有任何子类，String的底层是一个final 类型的char类型的数组。String一旦被创建就是固定不变的了，对String对象的任何操作都不会改变原对象。String对象可以通过双引号包裹的常量赋值，也可以通过new实例化对象赋值。常量赋值时内存指向的是常量池，new实例化对象时内存指向的是堆内存

### 2、String与 StringBuffer StringBuilder 区别 底层

首先三种字符串的类型都是被final修饰的，都是不可以被继承的。实例化对象时String类是可以直接常量赋值也可以实例化对象赋值。而StringBuffer和StringBuilder必须实例化对象。关于字符串的拼接，String可以使用+号或concat方法进行字符串拼接，String每次拼接都会返回新的字符串，都需要反复的开辟内存空间，这导致了String的拼接效率比较低。StringBuffer和StringBuilder是使用append方法进行追加的拼接操作，拼接不会返回新的字符串对象，不需要重新开辟内存，这样使二者的拼接效率高于String。最后是线程方面的控制，StringBuffer中的方法都使用了synchronized修饰，给方法添加了线程锁，使它在多线程环境的使用下也不会发生数据混乱。

### 3、String源码

String存在于java.lang包中，类是由final修饰的，所以不能被继承，底层没有继承任何类，但是实现了CharSequence，和序列化的接口，

底层变量

默认是一char的 常量数组，finla修饰不可改变，集合了多个字符，

变量的字符串 private final char value[];

String的hash码 private int hash; // Default to 0

底层方法

在无参构造中有 this.value = "".value; 意思是如果不赋值，就可以默认赋值一空字符串

在有参构造中是 this.value = original.value;， this.hash = original.hash;意思将值设置给空数组将hash重新赋值

在charAt方法中，根据下标取值，就是直接返回value[下标]

String变量使用方便但是效率低下，因为String 底层是常量数组不可改变，每次拼接的话，走底层concat方法，concat方法底层是new了一个新的String开辟了一个全新的 对象，所以大批量拼接用String buffer