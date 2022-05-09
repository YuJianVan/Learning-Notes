### Java线程状态

![image-20220426234732198](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220426234732198.png)





Lambda表达式相关

1. 接口中只能声明一个方法，才可以使用()->{}。
2. 接口只声明一个方法时自动添加以下注解(函数功能接口)

~~~java
@FunctionalInterface
~~~

3. 使用接口中使用default可实现多个函数。
4. 使用static也可在接口中实现多个函数。

示例代码：

~~~java
public class LambdaTest {
    public static void main(String[] args) {
        Calculator calculator= (x,y)->{return x+y;};
        System.out.println(calculator.add(3, 5));
        System.out.println(calculator.div(3, 5));
        System.out.println(calculator.mul(3, 5));
        System.out.println(Calculator.sub(4,3));
    }
}

@FunctionalInterface
interface Calculator{
    public int add(int x,int y);

    default int mul(int x,int y){
        return x * y;
    }

    default int div(int x,int y){
        return x / y;
    }

    public static int sub(int x,int y){
        return x - y;
    }
}
~~~

运行结果：

~~~
8
0
15
1
~~~



#### Callable

1. ###### Callable和Runnable的区别

   



### 可重入锁

>线程获取到对象的锁后，本线程可以再次获取该对象的锁，而其他线程不可以。
>
>synchronized和ReentrantLock都是可重入锁。



3种让线程等待和唤醒的方法：

方式1：使用Object中的wait()方法让线程等待，使用Object中的notify()方法唤醒线程

方式2：使用JUC包中Condition的await()方法让线程等待，使用signal()方法唤醒线程

方式3：LockSupport类可以阻塞当前线程以及唤醒指定被阻塞的线程





### 什么是线程上下文切换？

一个CPU同一时刻只能执行一个线程，当一个线程的时间片用完后进入阻塞状态，cpu执行另外一个线程。之前那个线程再次抢到时间片后，从之前的进度基础上继续执行。这个过程称为线程上下文切换。



### 上下文的内容？

寄存器的存储内容：CPU寄存器负责存储已经、正在和将要执行的任务
程序计数器存储的指令内容：程序计数器负责存储CPU正在执行的指令位置、即将执行的下一条指令的位置



### 如何减少线程上下文切换？

1. 线程不宜过多。
2. 使用无锁并发编程
3. 使用CAS算法更新数据
4. 使用协程。



### run()方法和start()方法有什么区别？

run()方法是线程中重写的方法，调用run()方法表示用主线程调用线程中的方法，并不能启动一个新线程。

start()方法才能启动一个新线程，让新线程执行重写的run()方法。



### 线程状态的变化：

start()后：Running

yield()后：Runnable

sleep()后：Time Waiting

wait()后：Time Waiting

join()后：Time Waiting



### sleep()和yield()方法的区别？

sleep()使得线程从Running状态变为Time Waiting阻塞态，会让出CPU。

yield()使线程从Running态变为Runnable就绪态，尝试让出CPU，不一定会让出，具体依赖操作系统的调度。

#### 应用：

使用sleep()或者yield()在循环中防止因死循环造成CPU 100%



### sleep()和wait()的区别？

![image-20220427234044364](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220427234044364.png)



### 线程优先级：

1. 优先级提示调度器优先调度优先级高的线程，但可能会被忽略。

2. cpu空闲时效果不明显。



### join()方法相关：

>join()底层通过wait()实现

t.join()表示主线程等待t线程执行完后再继续执行

t.join(time)表示主线程等待t线程的最大期限time毫秒，超过等待时间也会继续执行。



### ConcurrentHashMap

##### JDK7：

##### 创建时机：饿汉式

##### 数据结构：两层数组+链表

##### 初始容量(DEFAULT_CAPACITY)：16

##### 并发度(DEFAULT_CONCURRENCY_LEVEL)：16

##### 扩容因子(LOAD_FACTOR)：0.75



Segment数组(外层数组)长度=并发度

每个索引下的数组长度才受扩容因子影响，HashConcurrent数组(小数组)长度=容量/并发度

![image-20220423205653710](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220423205653710.png)

##### segment下标计算方法：

	1. 当前并发度为2的n次幂
	2.  取二次hash值得高n位作为segment值。



##### 小数组下标计算方法：

​	1. 二次hash值二进制数末位即为小数组下标



##### 扩容时机：超过(>)0.75*小数组总节点数





##### JDK8：

##### 创建实际：懒汉式

##### 扩容时机：达到(=)0.75*小数组总节点数

##### 数据结构：数组+链表

##### 初始容量(DEFAULT_CAPACITY)：16

##### 扩容因子(LOAD_FACTOR)：0.75

首次创建数组时，先创建>capacity*load_factor的数组大小，之后再按节点数满capacity * 0.75的规则扩容。



##### 扩容时并发put()操作：

![image-20220423231224108](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220423231224108.png)

三种情况：

1. 线程对迁移前的索引数组进行put(上图12345号节点)：正常向原数组中put

2. 线程对正在迁移的索引部分节点进行put(上图10号索引)：由于加了写锁，线程被阻塞

3. 线程对已经迁移的索引部分节点(上图11  12  13  14  15号索引节点)进行put操作：帮助其他线程迁移，迁移完成后才向新数组put



##### 扩容时并发get()操作：

两种情况：

1. 数组不为空，则在原数组get()；
2. 数组为空，在新数组中get()；



### Hashtable和ConcurrentHashMap

1. 都是线程安全的，key和value不能为null



### Hashtable

1. 初始容量11
2. 扩容因子0.75