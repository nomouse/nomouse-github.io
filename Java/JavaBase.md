# Java基础
## 什么是Java

## JVM/JRE/JDK关系
JVM是Java虚拟机(Java Virtual Machine)，是java程序运行的载体，通过实现不同操作系统的虚拟机可以实现java的跨平台。
JRE是Java运行时环境(Java Runtime Environment)包括Java虚拟机和Java程序所需的核心类库等。
JDK是Java开发工具(Java Development Kit)其中包含了Java的开发工具，也包括了JRE。所以安装了JDK，就无需再单独安装JRE了。其中的开发工具：编译工具(javac.exe)，打包工具(jar.exe)等

## Java基本数据类型

- 数值型
整数类型(byte,short,int,long),byte1字节，short2字节，int4字节，long8字节
浮点类型(float,double)
字符型(char)
布尔型(boolean)

- 引用数据类型
类(class)
接口(interface)
数组([])

## 访问修饰符
private : 在同一类内可见。使用对象：变量、方法。 注意：不能修饰类（外部类）
default (即缺省，什么也不写，不使用任何关键字）: 在同一包内可见，不使用任何修饰符。使用对象：类、接口、变量、方法。
protected : 对同一包内的类和所有子类可见。使用对象：变量、方法。 注意：不能修饰类（外部类）。
public : 对所有类可见。使用对象：类、接口、变量、方法


## 抽象类与接口的区别
抽象类是用来捕捉子类的通用特性的。接口是抽象方法的集合。
从设计层面来说，抽象类是对类的抽象，是一种模板设计，接口是行为的抽象，是一种行为的规范。


## 重载与重写的区别
方法的重载和重写都是实现多态的方式，区别在于前者实现的是编译时的多态性，而后者实现的是运行时的多态性。
重载：发生在同一个类中，方法名相同参数列表不同（参数类型不同、个数不同、顺序不同），与方法返回值和访问修饰符无关，即重载的方法不能根据返回类型进行区分
重写：发生在父子类中，方法名、参数列表必须相同，返回值小于等于父类，抛出的异常小于等于父类，访问修饰符大于等于父类（里氏代换原则）；如果父类方法访问修饰符为private则子类中就不是重写。


## ==和equals
==对比对象引用(或者说是地址)，equals对比对象的内容

## hashCode与equals
hashCode() 的作用是获取哈希码，也称为散列码。

## 值传递和引用传递有什么区别
值传递：指的是在方法调用时，传递的参数是按值的拷贝传递，传递的是值的拷贝，也就是说传递后就互不相关了。
引用传递：指的是在方法调用时，传递的参数是按引用进行传递，其实传递的引用的地址，也就是变量所对应的内存空间的地址。传递的是值的引用，也就是说传递前和传递后都指向同一个引用（也就是同一个内存空间）。

## String特点
字符串常量池

## Integer和Long类型比较大小
存在缓存，在缓存范围可以用==，超出需要用equals


# Java集合
## fail-fast和fail-safe
是java集合的一种错误检测机制，当多个线程对集合进行结构上的改变的操作时，有可能会产生fail-fast机制
解决办法：
1.在遍历过程中，所有涉及到改变modCount值得地方全部加上synchronized。
2.使用CopyOnWriteArrayList来替换ArrayList

## List
### ArrayList
底层是数组，适合随机读和顺序写，对于删除和插入需要元素复制操作，性能较差。

### LinkedList

### 为什么 ArrayList 的 elementData 加上 transient 修饰

## Set
### HashSet

## Queue
### 基础
单向队列
add()：若超出了度列的长度会直接抛出异常：
offer()：如果发现队列已满无法添加的话，会直接返回false。

poll()：若队列为空，返回null。
remove()：若队列为空，抛出NoSuchElementException异常。
### Deque
双向队列

### BlockingQueue
put()：若向队尾添加元素的时候发现队列已经满了会发生阻塞一直等待空间，以加入元素。
take()：若队列为空，发生阻塞，等待有元素。

### PriorityBlockingQueue
优先级队列，内部使用堆排序，基于最小二叉堆实现

### SynchronousQueue
同步队列，在线程池中如果并发线程超过核心线程，不想要进入队列想要直接扩展核心线程时使用。

## Map
### HashMap
哈希表

### TreeMap
排序哈希表

### LinkedHashMap

### ConcurrentHashMap
原理:
1.7之前是分段锁Segment+HashEntry
1.8之后是Node+CAS+Synchronized