执行顺序

静态资源==>



## IO流

>#### 类名以Stream结尾的都是字节流，以“Reader/Writer"结尾的都是字符流

#### 四种类型

##### java.io.InputStream    字节输入流

##### java.io.OutputStream   字节输出流

##### java.io.Reader         字符输入流

##### java.io.Writer         字符输出流

##### 以上四个都是抽象类，

1. ##### 都实现了java.io.Closeable接口，都可以关闭(close()方法)，使用完都需要关闭管道
2. ##### 都实现了java.io.Flushable接口，都可以刷新(Flush()方法)，输出后需要刷新，将剩余

  ##### 内容强制输出，否则可能造成数据丢失。



```java
文件专属：
FileInputStream
FileOutputStream
FileReader
FileWriter

转换流：
InputStreamReader
OutputStreamWriter

缓冲流专属：
BufferedReader
BufferedWriter
BufferedInputStream
BufferedOutputStream

数据流专属：
DataInputStream
DataOutputStream

标准输出流：
PrintWriter
PrintStream

对象专属流：
ObjectInputStream
ObjectOutputStream
```



### FileInputStream

常用方法：

```java
read() 一次读一个字节,返回字节的ascii码，读完返回-1
read(byte[]) 一次读byte数组长度的字节数，返回读取的字节数，读完返回-1

int available():返回流当中剩余的没有读到的字节数量
long skip(long n):跳过几个字节不读
```

使用示例：

```java
 FileInputStream fis=null;
        try{
            fis=new FileInputStream("绝对路径地址");//绝对路径
        	//准备一个byte数组
        	byte[] bytes=new byte[10];
        	int readCount; // 读到的字节数
        	// readCount如果返回-1说明已经读完了
        	while((readCount=fis.read(bytes))!=-1){
            	System.out.println(new String(bytes,0,readCount));
        	}
        }catch(FileNotFoundException e){
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 避免空指针异常
            if(fis!=null){
                try{
                    fis.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
```

### FileOutputStream

常用方法：

##### write(byte[])  写入字节数组

##### flush()   刷新

##### new FileOutputStream("文件地址")    清空原文件，重写内容

##### new FileOutputStream("文件地址",true)   以追加的方式写入，不清空原本内容。

使用示例：

```java
public static void main(String[] args) {
    FileOutputStream fos=null;
    try {
        //文件不存在时会自动新建(覆盖写入，不能接着写)
        //fos=new FileOutputStream("文件地址");
        //以追加的方式写入(FileOutputStream("",true))
        fos=new FileOutputStream("文件地址",true);

        /**
         * 回车，ASCII码13，"\r"
         * 换行，ASCII码10，"\n"
         * 空格，ASCII码32
         */
        //设置写入内容
        byte[] bytes3 ={97,98,99,100,101,102,10};
        //写出byte数组
        fos.write(bytes3);

        //部分写出byte数组
        fos.write(bytes3,0,2);
        fos.write(bytes3,6,1);

        //写完之后必须刷新
        fos.flush();

    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }finally{
        if(fos!=null){
            try {
                fos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```





### 类成员访问控制权限

![image-20220426162554061](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220426162554061.png)