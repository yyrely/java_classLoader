# java一个程序的完整过程
## 编写期

> 这是我自己的一个说法   
当我们打开我们熟悉的编程工具  
开始一段一段代码的编写,这就是我所说的编写期

```java
public class HelloWorld{

    public static void main(String[] args){
        System.out.println("Hello World");
    }
}
```

>这是我们最熟悉不过的代码了,每个程序员不写个Hello World就全身不舒.  
写了这个代码以后,我们存放文件的目录下会有一个HelloWrold.java的文件,我们的编写期就完成啦

## 编译期
> HelloWord.java这个文件是给我们程序员看的  
接下来我们就是要把它变成计算机可以看懂的代码

```text
    在windows系统下,打开cmd,进入HelloWorld.java所在的文件夹
    输入  javac HelloWorld.java   命令
```

> 这是将HelloWorld.java文件编译成HelloWorld.class文件,这就是一个机器可以读得懂的文件  
需要解析它的就是java虚拟机了  
这个过程是一个翻译的过程,在这个过程里也会检查我们写的代码是不是有错

```java
    static final String a = "Hello World"
```
> 这是一个编译期常量  
static表示这个属性只能有一个  
final表示这个属性赋值后就不能更改了  
所以这个一个编译期就确定的变量  
在以后的调用时,和这个属性所在的类是没有关系的,并不会去加载这个类(下面要说的加载期)  
在网上看到一个使用编译期常量的注意事项:  
在更新编译期常量时,一定要重新编译整个程序,不然会出现读取变量的值不同的风险

## 加载期(类加载过程)
```text
    在上面生成HelloWorld.class文件的目录下
    输入    java HelloWorld    命令
```
> 这样java虚拟机就会将HelloWorld.class文件加载到内存中进行加载  
这我们就要说一下java的类加载机制了  
类加载过程一共分为五个部分,如下图:

![类加载过程](https://github.com/yyrely/java_classLoader/blob/master/%E7%B1%BB%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B.png)

* 加载(重点)
```text
加载时类加载过程的第一个阶段，在加载阶段，虚拟机需要完成以下三件事情：

1、通过一个类的全限定名来获取其定义的二进制字节流。

2、将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构。

3、在Java堆中生成一个代表这个类的java.lang.Class对象，作为对方法区中这些数据的访问入口。

换简单的话来说,就是通过HelloWorld的完整类名,去寻找这个类生产的.class二进制文件,将其加载到内存中.
* 这个类有关的信息都存在方法区中
    - 类的全路径名
    - 这个类父类的全路径名
    - 类的类型(类或接口)
    - 类的访问修饰符(public,private等)
    - 常量池(字段,方法信息(静态方法,非静态方法),静态变量,类型引用等)

堆中会生成一个该类的java.lang.Class对象,作为入口去访问方法区中存储的数据
```
![类信息存放](https://github.com/yyrely/java_classLoader/blob/master/%E7%B1%BB%E4%BF%A1%E6%81%AF%E5%AD%98%E6%94%BE.png)

```text
在加载时,类的静态方法就已经存在方法区中了,在堆中生成了对应的对象,所以通过这个对象就可以直接访问静态方法了.如我们平时使用的: 类名.静态方法名()

而非静态方法也是存在方法区中的,它需要对象实例化后,在调用需要的方法是,会从方法区中找到对应的方法,将其加载到栈帧中进行执行.

将非静态方法存于方法区中,可做到复用,每次实例化一个新的对象时,调用的方法都是从方法区中找的.不至于每次实例化一个对象,都要重新加载一次对象的方法,这样会给内存带来很大的压力.
```

类加载器
```text
说到了类到的加载,会好奇它到底是怎么加载的,是通过什么进行加载的.这就要看看类加载器了

BootstrapClassLoader
ExtClassLoader
ApplicationClassLoader

这是三种默认使用的类加载器,做一下简单的介绍
```

BootstrapClassLoader
> 称为启动类加载器,是java类加载器中最顶层的类加载器,也是负责加载JDK核心类库的
```java
    //其加载的核心类库
    public static void main(String[] args) {

        URL[] urls = Launcher.getBootstrapClassPath().getURLs();
        for(URL url : urls){
            System.out.println(url);
        }
    }

    结果:
    file:/C:/Program%20Files/Java/jdk1.8.0_102/jre/lib/resources.jar
    file:/C:/Program%20Files/Java/jdk1.8.0_102/jre/lib/rt.jar
    file:/C:/Program%20Files/Java/jdk1.8.0_102/jre/lib/sunrsasign.jar
    file:/C:/Program%20Files/Java/jdk1.8.0_102/jre/lib/jsse.jar
    file:/C:/Program%20Files/Java/jdk1.8.0_102/jre/lib/jce.jar
    file:/C:/Program%20Files/Java/jdk1.8.0_102/jre/lib/charsets.jar
    file:/C:/Program%20Files/Java/jdk1.8.0_102/jre/lib/jfr.jar
    file:/C:/Program%20Files/Java/jdk1.8.0_102/jre/classes
```

> 还有一个奇怪的现象
```java
    public static void main(String[] args) {

        System.out.println(String.class);
        System.out.println(String.class.getClassLoader());

        ClassLoader classLoader = sun.security.ec.SunEC.class.getClassLoader();
        System.out.println(classLoader);
        System.out.println(classLoader.getParent());
    }

    结果:
    class java.lang.String
    null
    sun.misc.Launcher$ExtClassLoader@5cad8086
    null  
```
> 加载String.Class的类加载器是BootstrapClassLoader  
ExtClassLiader类加载器的父类加载器也是BootstrapClassLoader  
但是两次结果输出的都是null,这就有点奇怪了  
查询一番资料,得知BootstrapClassLoader的底层实现是由C/C++编写的,并不是一个java类,在java代码中不能获取到它的引用,所以两次输出的结果都是null

ExtClassLoader
> 扩展类加载器,默认加载JAVA_HOME/jre/lib/ext/目录下的所有jar包

ApplicationClassLoader
> 应用加载器,也称为系统加载器,负责在JVM启动时,加载来自在命令java中的classpath或者java.class.path系统属性或者CLASSPATH操作系统属性所指定的JAR类包和类路径.  
平时我们写的类基本都是它来加载的
```java
    //写的一个普通测试类
    public class Test {

        public static void main(String[] args) {
            System.out.println(Test.class.getClassLoader());
        }
    }

    结果:
    sun.misc.Launcher$AppClassLoader@18b4aac2
```

类加载器的工作原理
> 类加载器的工作原理基于三个机制:  
双亲委派机制  
可见性机制  
单一性机制

双亲委派机制
```text
java类加载器加载类的顺序是从上向下的,就默认的三个类加载器来说  
加载一个Test.class 

1.先拿到这个二进制文件的是ApplicationClassLoader  
2.到手后什么都不做,直接扔给父加载器ExtClassLoader  
3.同样的,ExtClassLoader拿到后,也直接扔给它的父加载器BootstrapClassLoader  
4.因为BootstrapClassLoader是最顶层的类加载器,它要试着加载该文件,看自己是否可以加载  
5.不能解析则还给它的子类加载器ExtClassLoader,它也试着加载该文件  
6.还是不能则还给了ApplicationClassLoader,Test.class是我们写的类,由它负责加载,它可以完成加载
7.如果ApplicationClassLoader也不同完成加载,会抛出ClassNotFind的异常

这样做有什么好处呢

可以防止我们在编写程序时,创建了一个java.lang.String的类而覆盖了java核心包中原本已经写好的java.lang.String类.
防止核心包中的类被随意篡改,而导致的代码混乱不清
```

可见性机制

```text
父类加载器加载的类,子类是可以看到的,反之不能
```
```java
package chuncongcong.lambda;

/**
 * @author Hu
 * @date 2018/10/13 10:45
 */

public class Test06 {

    public static void main(String[] args) throws ClassNotFoundException {

        System.out.println(Test06.class.getClassLoader());

        Class.forName("chuncongcong.lambda.Test06",true,Test06.class.getClassLoader().getParent());
    }
}

结果:
sun.misc.Launcher$AppClassLoader@18b4aac2
Exception in thread "main" java.lang.ClassNotFoundException: chuncongcong.lambda.Test06
	at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
	at java.lang.Class.forName0(Native Method)
	at java.lang.Class.forName(Class.java:348)
	at chuncongcong.lambda.Test06.main(Test06.java:14)
```

单一性机制
```text
同一个类只能被加载一次,父类加载器加载过的类,子类加载器时不同在进行加载的
存在可以加载的情况,但是不建议
```

* 连接
```text
连接分为三步:
1.验证
2.准备
3.解析
```
验证
```text
    确保class字节流的信息是否正确,是否会危害到虚拟机

    文件格式验证：验证字节流是否符合Class文件格式的规范；例如：是否以0xCAFEBABE开头、主次版本号是否在当前虚拟机的处理范围之内、常量池中的常量是否有不被支持的类型。

    元数据验证：对字节码描述的信息进行语义分析（注意：对比javac编译阶段的语义分析），以保证其描述的信息符合Java语言规范的要求；例如：这个类是否有父类，除了java.lang.Object之外。

    字节码验证：通过数据流和控制流分析，确定程序语义是合法的、符合逻辑的。

    符号引用验证：确保解析动作能正确执行。
```

准备
```text
    正式为类变量(static)分配内存并附默认值,分配的内存是在方法区中的

    1.分配内存的仅仅是类变量,也就是static变量,实例变量是在对象实例化时和对象一起分配内存在堆中的

    2.类变量当前只是赋上默认值,8大基本类型是各自对应的默认值,引用对象默认为null

    3.若是存在static final编译期常量,直接为其附上值,如 static final String a = "123",在这一过程中a默认为"123"
```

解析
```text
    将符号引用解析成直接引用

    这个部分不是特别理解,就用我自己的话来说说吧

    符号引用: 在一个类(a)中我们经常会调用另一个类(b),b这个类是作为包import进来的,在a.class刚刚被加载到内存中时,这些信息都是作为字面量存储的,在方法区中会有一块地方是存储a类有关的信息,而b这个字面量,就是一个符号引用.

    直接引用: b有了自己的内存地址,符号引用指向了这个地址,就有了意义,应该就解析成直接引用了
```

* 初始化
```text
    jvm开始为类变量赋初值,jvm会执行<client>()方法,收集类中所有静态变量,静态块于该方法中,进行执行
    1.类变量(静态变量)
    2.静态块中的语句
```
初始化顺序
```text
    1.没有加载和连接的类先加载,连接

    2.父类没有被初始化,先初始化父类类变量

    3.静态变量和静态块按编写顺序从上向下执行
```
触发初始化的时机(生成< client> ()方法)
```text
    1.实例化一个没有被加载过的类(new)

    2.调用该类的类变量

    3.调用该类的静态方法

    4.反射

    5.初始化其子类,其父类也会被初始化
```
初始化不被触发的的情况(不生成< client>()方法)
```text
    1. 通过子类调用其父类的类变量,只初始化父类初始化子类

    2. 调用类中static final变量,该类不初始化

    3. 定义对象数组,该数组中的对象不会初始化 
        User[] = new User[2]; => User user1,user2;

    4. 通过类名获取Class对象,该类不被初始化

    5. 通过Class.forName()加载指定类时,initialize赋值为false,该类不初始化,false的意思就是不初始化

    6.通过ClassLoader默认的loadClass方法加载类,该类不被初始化
```
* 使用
* 卸载

```text
以上就是一个程序从编写到加载的整个过程了

一个程序由多个类组成,每个类在第一次用到时都会被加载,也只加载一次

这样这个类的相关信息就在jvm的方法区中存储好了,再一次使用就会直接去方法区中找

类加载器还有很多更深入的使用,有兴趣的可以去深入了解一下,这里只是讲了皮毛

也算总结了一下自己对类加载过程的理解,有说错的,请大佬指出
```

[参考文章](http://www.importnew.com/23742.html)
