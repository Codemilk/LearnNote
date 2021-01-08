---
typora-root-url: ..\..\Pic
---

# File类

## 1.File类的使用

* File类的一个对象，代表一个文件或一个文件目录
* File类声明在java.io包下
* File类中涉及到关于文件或文件目录的创建、删除、重名名、修改时间】文件大小等方法，并未涉及到写入或读取文件内容的操作。如果需要读取或写入文件内容，必须用IO流来完成
* 后续File类的对象常会对照参数传递到流的构造器中，指明读取或写入的终点

## 2.File的实例化

### 2.1常用构造器

File(String FilePath)；

File(String parentPath,String childPath)；

File(File parentFile,String parentPath)；



### 2.2路径的分类

* 相对路径：相较于某个路径下，指明的路径，通过查看方法的调用者
* 绝对路径：包含盘符在内的文件或文件目录的路径

注意：当你在main方法中运行的时候，就会从项目路径中查找路径，在@Test方法中测试的时候起始在当前模块

![image-20201222125012982](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201222125015.png)



### 2.2路径分割符

windows和Dos系统默认使用"\\"来表示

UNIX和URL默认使用"/"

### 2.3File类的常用方法

![image-20201222131537876](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201222131537.png)

![image-20201222131806340](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201222131806.png)

![image-20201222131732595](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201222131732.png)





# IO流原理及流的分类

* I/O是Input/Output的缩写，IO技术是非常使用的技术，用于处理设备之间的数据传输，如：如些文件，网络通讯
* Java程序中，糴数据的输入输出操作以“流”的方式进行
* java.io包下提供了各种流类和接口，用以获取不同种类的数据，并通过标准的方法输入或输出数据

## JavaIO原理

我们对于输入输出，要站在程序（内存）的角度

* 输入（input）：读取外部数据（磁盘，光盘等存储设备的数据）到程序（内存）
* 输出（output）: 将内存数据输出到磁盘，光盘等存储设备中



## 流的分类

| 流的类型 | 输入流(inputStream) | 输出流(outputStream)      |
| -------- | ------------------- | ------------------------- |
| 数据单位 | 字节流(1字节=8bit)  | 字符流（1字符等于两字节） |
| 流向     | 输入                | 输出                      |
| 角色     | 节点流              | 处理流                    |

1. java的IO一共40多个类，实际上非常规则，都是从如下4个抽象基类派生的

   ![image-20201222141045684](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201222141046.png)

2. 由着四个类派生出来的子类名称都是以其父类名作为子类名后缀

![image-20201222142016882](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201222142016.png)





## 流的体系结构

![image-20201222142615939](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201222142616.png)

**重点关注蓝色标记**

## 字符流

### 文件字符流

#### FileReader

```java
package File;


import com.sun.org.apache.bcel.internal.classfile.Code;
import org.junit.jupiter.api.Test;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

/**
 * 一、流的分类
 * 1.操作数据单位：字符，字节流
 * 2.数据流向：输入输出
 * 3.角色：节点，处理流
 * <p>
 * <p>
 * 二、体系结构
 * 四个抽象基类         节点流(直接对文件操作)         缓冲流（处理流）
 * InputStream        FileInputStream            BufferedInputStream
 * OutputStream       FileOutputStream           BufferedOutputStream
 * Reader             FileReader                 BufferedReader
 * Writer             FileWriter                 BufferedWriter
 *
 * @author lenovo
 */
public class FileReaderWriterTest {

    /**
     * 使用相对路径，但是起始点在当前方法也就是当前的模块，如果在main方法中，那么起点就是在项目根节点
     */
    @Test
    public void test() {
        //1.读入文件
        File file = new File("src/main/resources/hello.txt");

        FileReader fileReader = null;
        try {


            //2.提供具体的流
            fileReader = new FileReader(file);

            //3.数据的读入
            //返回的是字符的对应数字，通过read往下读，知道返回-1结束
            int data;

            while ((data = fileReader.read()) != -1) {

                System.out.println((char) data);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {//4.流的关闭
            if (fileReader != null) {
                try {
                    fileReader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }


    }

}

```

**read(array)**


```java
package File;


import com.sun.org.apache.bcel.internal.classfile.Code;
import org.junit.jupiter.api.Test;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

/**
 * 一、流的分类
 * 1.操作数据单位：字符，字节流
 * 2.数据流向：输入输出
 * 3.角色：节点，处理流
 * <p>
 * <p>
 * 二、体系结构
 * 四个抽象基类         节点流(直接对文件操作)         缓冲流（处理流）
 * InputStream        FileInputStream            BufferedInputStream
 * OutputStream       FileOutputStream           BufferedOutputStream
 * Reader             FileReader                 BufferedReader
 * Writer             FileWriter                 BufferedWriter
 *
 * @author lenovo
 */
public class FileReaderWriterTest {


    /**
     * 对read()操作升级:使用read的重载方法
     * */
    @Test
    public void testFileReader2(){

        File file = new File("src/main/resources/hello.txt");
        FileReader fr=null;
        try {

        fr = new FileReader(file);

        char [] array =new char[5];
        int e;

        //read(array)表示读入操作 传入参数为char型数组:返回每次读入数组中的个数,整个函数表示每次读入5个字符就进入一次循环，不足五个就有几个读几个
        while ((e=fr.read(array))!=-1){

            System.out.println("循环开始");
            //这里不可以是i<array.length，因为当最后一次遍历不足五个，就会使用上一个数组的是，说白了就是覆盖的一个过程，前几个你有值不去覆盖，后面你没法覆盖
            for (int i = 0; i <e;i++){
                System.out.println(array[i]);
            }
        }

        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            if (fr != null) {
                try {
                    fr.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }


    }
}

```

运行结果：

![image-20201222191828769](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201222191829.png)





#### FileWriter

```java
 /**
     * 说明：
     *  1. 输出操作，对应的File可以不存在，若不存在，在输出的过程自己创建
     *              如果存在那么就是，文件的选择覆盖：创建流的是否在构造器参数为new FileWriter(file,false)和new FileWriter(file)表示覆盖
     *                                          创建流的是否在构造器参数为new FileWriter(file,true)表示接着文件追加
     * */
    @Test
    public void testFiLeWriter() throws IOException {
        //1.准备文件
        File file = new File("src/main/resources/hello1.txt");

        //2.准备流,提供FileWriter的对象，用于数据的写出，就是写入到这个文件
        FileWriter fileWriter = new FileWriter(file,true);

        //3.写出
        fileWriter.write("I have a dream！");
        fileWriter.write("Me too！");

        //4.流的关闭
        fileWriter.close();
    }
```



#### 通过FileReader和FileWriter来实现文本的复制

```java
import com.sun.org.apache.bcel.internal.classfile.Code;
import org.junit.jupiter.api.Test;

import java.io.*;

/**
 * 一、流的分类
 * 1.操作数据单位：字符，字节流
 * 2.数据流向：输入输出
 * 3.角色：节点，处理流
 * <p>
 * <p>
 * 二、体系结构
 * 四个抽象基类         节点流(直接对文件操作)         缓冲流（处理流）
 * InputStream        FileInputStream            BufferedInputStream
 * OutputStream       FileOutputStream           BufferedOutputStream
 * Reader             FileReader                 BufferedReader
 * Writer             FileWriter                 BufferedWriter
 *
 * @author lenovo
 */
public class FileReaderWriterTest {
    /**
     * 通过 FileReader 和  FileWriter来进行文本复制,以后在程序中大部分在网络上运行,所以要自己处理,不能让程序直接停下来
     * */

    @Test
    public void testFiLeWriterAndFileReader() throws IOException {

        FileReader fileReader =null;
        FileWriter fileWriter =null;
        try {
            // 创建文件
            File CopyFile = new File("src/main/resources/CopyFile.txt");
            File PasteFile=new File("src/main/resources/PasteFile.txt");

           fileReader = new FileReader(CopyFile);
           fileWriter = new FileWriter(PasteFile,true);

            char[] array=new char[5];

            int i=0;

            while((i=fileReader.read(array))!=-1){

                fileWriter.append(String.valueOf(array));
            }

        } catch (IOException e) {
            e.printStackTrace();
        }finally {

            try {
                if (fileWriter != null) {

                fileWriter.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                if (fileReader != null) {

                fileReader.close();
                }
            }
        }

    }
}


```



**注意：不可以使用文件字符流来处理图片等字节数据 **





##  文件字节流

### FileinputStream

```java
import org.junit.jupiter.api.Test;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

/**
 * 测试FileOutputStream 和 FileInputStream
 *
 * 结论：
 *   1.对于文本文件（.txt、.java、.c、.c++.......），使用字符流处理流
 *   2.对于非文本文件（.jpg、.mp3、.avi、.ppt......），使用字节流处理流
 *
 * @author lenovo
 */
public class FileOutInputTest {

    @Test
    public void testFileInputTest(){
        File file = new File("src/main/resources/hello.txt");


        FileInputStream fileInputStream=null;
        try {
            //建立流
            fileInputStream = new FileInputStream(file);

            byte[] bytes = new byte[5];
            //读取数据
            //记录每次读取的字节的个数
            int len;
            while ((len=fileInputStream.read(bytes))!=-1){

                /**从0索引开始解码，len表示长度,用作字节数组解码,这里复习一下，在英文里面英文字母对应的Ascall码对应的值字节可以存下，所以可以说一个
                 * 字节就是一个英文字母，但是中文就不可以，因为不同的编码形式，中文所占的字节数不一样，在utf-8中,中文站三个字节
                 * */
                String s = new String(bytes, 0, len);
                
                System.out.print(s);

            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (fileInputStream != null) {
                fileInputStream.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

}

```



### FileOutputStream和FileInputStream实现非文本的赋值

```java
import jdk.management.resource.internal.inst.SocketOutputStreamRMHooks;
import org.junit.jupiter.api.Test;

import java.io.*;

/**
 * 测试FileOutputStream 和 FileInputStream
 *
 * 结论：
 *   1.对于文本文件（.txt、.java、.c、.c++.......），使用字符流处理流
 *   2.对于非文本文件（.jpg、.mp3、.avi、.ppt......），使用字节流处理流
 *
 * @author lenovo
 */
public class FileOutInputTest {
   
    /**通过文件字流对非文本文件操作*/
    @Test
    public void testFileInputTest1(){

        File test = new File("src/main/resources/test.png");
        File over = new File("src/main/resources/over.png");

        FileInputStream fileInputStream=null;
        FileOutputStream fileOutputStream = null;
        try {
            fileInputStream= new FileInputStream(test);
            fileOutputStream=new FileOutputStream(over);

            byte[] b = new byte[1024];
            int len;
            while((len=fileInputStream.read(b))!=-1){

                fileOutputStream.write(b,0,len);

            }


        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                fileInputStream.close();
                fileOutputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }


    }
}

```






