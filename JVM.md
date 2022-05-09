方法区存放类及其相关信息，对象存放在堆中，变量、参数存放在虚拟机栈
线程私有 程序计数器和虚拟机栈
线程共享 堆和方法区

程序计数器的作用，存放下一条指令的地址。

--什么是类加载器？
类加载器负责加载class文件，把文件中字节码内容加载到内存中，将内容转换成方法区中的运行时数据结构。
类加载器只负责加载class文件，能否运行有Execution Engine决定。
类加载器自顶向下分别为：启动类加载器--扩展类加载器--应用程序类加载器--用户自定义加载器


--什么是双亲委派机制？
当一个类收到类加载请求，先不尝试自己加载，而是把请求委派给父类加载，
每层加载器向父类委派，知道顶层的启动类加载器。当父类加载器反馈无法完成请求(也就是加载路径下没有所需的Class，子类才会自己加载。

--双亲委派机制的好处？
保证使用不同的类加载最终得到的是同一个对象。保证了java环境的沙箱安全，防止污染源代码。比如用户创建java.lang.String类，由于有
双亲委派机制，最终会交给父类加载器加载，这样保证了程序只会有同一个java自带的String类。

--什么是方法区？
线程共享的内存，存储了每一个类的结构信息(常量池，字段，构造函数等)典型是永久代/元空间。

--栈
主管java程序运行，线程创建时创建，生命周期随线程的生命周期，是线程私有的。
8种基本类型的变量+对象的引用变量+实例方法都在栈内存中分配。
栈帧保存三种类型：
StackOverFlowerError是错误不是异常

--堆、栈、方法区的交互关系？
HotSpot虚拟机通过指针访问对象，堆中存放对象实例以及类结构信息的地址，方法区存放对象类型数据。

--堆的组成？
新生代：伊甸园区，幸存区  
老年代
(所有的类在伊甸园区被new出来，伊甸园区满了之后，触发Minor GC进行回收后，将剩余对象转移至幸存0区，
0区满了之后，再次进行垃圾回收，再转移至1区。1区满了再转移至老年代。老年代满了之后进行Major GC进行垃圾回收，
剩余空间仍然不够，则抛出OOM错误【OutOfMemoryError】 )
MinorGC具体过程：GC清除伊甸园区和From区的垃圾后将剩余对象复制至to区，清空伊甸园区和From区之后再将to区的对象复制回From区
，如此循环15次后仍然留下的对象才转移至老年代。

出现OOM错误的原因：
1.java虚拟机堆内存设置得过小。
2.程序中创建了过多对象，不能被垃圾收集器收集。

补充：
JVM中只有程序计数器不会内存溢出，其他区域都有可能会溢出。
OutOfMemoryError：
1) 堆内存耗尽  (对象过多不能被GC回收)
2) 方法区加载的类过多。
3) 虚拟机栈线程数过多且未被销毁(每个线程最多1M内存)
StackOverflowError：
虚拟机栈方法调用次数过多

堆空间比例：
新生代：老年代=1 ：2
伊甸园：from(幸存0区)：to(幸存1区) = 8 ：1 ：1

--永久代/元空间在方法区(方法区又称为非堆内存)
常驻内存，用于存放JDK自身携带得Class、Interface的元数据
即存储的是运行环境所必须的类信息，不会被GC回收，关闭JVM才释放。



#### 四种引用

###### 1.强引用

最常见的普通对象引用，内存不足时，即使出现OOM也不会对强引用对象进行回收。强引用是造成java内存泄漏的主要原因之一。

代码：

~~~java
public class StrongRefrenceTest {
    public static void main(String[] args) {
        Object obj1=new Object();
        Object obj2=obj1;
        obj1=null;
        System.gc();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            System.out.println(obj2);
        }
    }
}

// 结果：java.lang.Object@3b07d329
~~~



###### 2. 软引用

当内存不足时，GC把软引用对象回收，内存充足则不回收。

~~~java
package interviewTest.JVM;

import java.lang.ref.SoftReference;

public class SoftReferenceTest {
    public static void main(String[] args) {
        //memoryEnough();
        memoryNotEnough();
    }

    public static void memoryEnough(){
        Object obj1=new Object();
        SoftReference<Object>softReferenceObj=new SoftReference<>(obj1);
        obj1=null;
        System.gc();
        System.out.println(softReferenceObj.get());
    }
    // 结果：java.lang.Object@3b07d329

    public static void memoryNotEnough(){
        Object obj1=new Object();
        SoftReference<Object>softReferenceObj=new SoftReference<>(obj1);
        System.out.println(obj1);  //java.lang.Object@3b07d329
        System.out.println(softReferenceObj.get());  //java.lang.Object@3b07d329

        obj1=null;

        // 设置5MB内存：Run->Edit Configuration ->VM options：-Xms5m -Xmx5m -XX:+PrintGCDetails
        try{
            //OutOfMemoryError
            byte[] oomByte=new byte[30*1024*1024];//30MB的数组
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            System.out.println(softReferenceObj.get()); //null
        }
    }
}

~~~

使用场景：加载图片时，多次读取硬盘速度慢，一次加载容易OOM，此时适合用软引用。

目的：降低OOM的概率



###### 3. 弱引用

不管内存够不够，一旦发生GC回收，则一定回收弱引用对象。

~~~java
public class WeakReferenceTest {
    public static void main(String[] args) {
        Object obj=new Object();
        WeakReference<Object> weakReference=new WeakReference<>(obj);
        System.out.println(obj);
        System.out.println(weakReference.get());

        obj=null;
        System.gc();

        System.out.println("------after GC-------");
        System.out.println(weakReference.get()); //null
    }
}
~~~



##### WeakHashMap：

~~~java
public class WeakHashMapTest {
    public static void main(String[] args) {
        hashMapTest();
        weakHashMapTest();
    }

    private static void hashMapTest(){
        HashMap<Integer,String>hashMap=new HashMap<>();
        Integer key=new Integer(1);
        String value="HashMap";
        hashMap.put(key,value);
        System.out.println(hashMap);  // {1=HashMap}

        key=null;
        System.gc();

        System.out.println(hashMap);  // {1=HashMap}
    }

    private static void weakHashMapTest(){
        WeakHashMap<Integer,String> weakHashMap=new WeakHashMap<>();
        Integer key=new Integer(2);
        String value="WeakHashMap";
        weakHashMap.put(key,value);
        System.out.println(weakHashMap);   // {2=WeakHashMap}

        key=null;
        System.gc();

        System.out.println(weakHashMap);  // {}
    }
}
~~~

运行结果：

~~~
{1=HashMap}
{1=HashMap}
{2=WeakHashMap}
{}
~~~





###### 4. 虚引用

形同虚设，每次get()都会返回null，任何时候都可能被GC回收，不能单独使用也不能返回对象，必须与引用队列联合使用，回收完成后会放入队列中。

作用：跟踪对象被垃圾回收的状态，在对象被回收时提供系统通知或者添加进一步处理流程，类似于AOP的after

~~~java
public class PhantomReferenceTest {
    public static void main(String[] args) {
        Object obj=new Object();
        ReferenceQueue<Object> referenceQueue=new ReferenceQueue<>();
        PhantomReference<Object>phantomReferenceObj=new PhantomReference<>(obj,referenceQueue);

        System.out.println(obj);  //java.lang.Object@3b07d329
        System.out.println(referenceQueue.poll()); // null
        System.out.println(phantomReferenceObj.get());  // null

        obj=null;
        System.gc();

        System.out.println("-----after GC-----");
        System.out.println(referenceQueue.poll());  // java.lang.ref.PhantomReference@41629346
        System.out.println(phantomReferenceObj.get());  // null
    }
}
~~~



运行结果：

~~~
java.lang.Object@3b07d329
null
null
-----after GC-----
java.lang.ref.PhantomReference@41629346
null
~~~



<img src="C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220424004234652.png" alt="image-20220424004234652" style="zoom:67%;" />