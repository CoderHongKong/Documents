# <center> jvm学习笔记 </center>
___
## 一、类的加载、链接、初始化
### 1、类的加载

![image](https://raw.githubusercontent.com/CoderHongKong/jvm-study/80e4b0c49b302e7acaf3cba26632777ae37574c6/src/main/resources/img/%E7%B1%BB%E5%8A%A0%E8%BD%BD%20.png)
- 加载：查找并加载二进制数据
	- 类的加载：指的是将类的.class 文件中的二进制数据读入到内存中，将其放在运行时数据区的方法区内，然后在内存中创建一个java.lang.Class对象（规范并未说明Class对象位于哪里，HotSpot虚拟机将其放在了方法区中）用来封装类在方法区内的数据结构
	- 加载.class文件的方式
		- 从本地系统中直接加载
		- 通过网络下载.class文件
		- 从zip，jar等归档文件中加载.class文件
		- 从专有数据库中提取.class文件
		- <font color="red">将Java文件动态编译为.class文件</font> 
- 链接：
    + 验证：确保被加载的类的正确性
    + 准备：为类的<font color="red">静态变量</font>分配内存,并将其初始化为<font color="red">默认值</font>
    + 解析：<font color="red">把类中符号引用转换为直接引用</font>
- <font color="red">初始化：为类的静态变量赋予正确的初始值</font>

### 2、类的使用方式
- java程序对类的使用方式可分为两种：
	+ 主动使用（7种）
		- 创建类的实例
		- 访问某个类或接口的静态变量（使用助记符：getstatic），或者对该静态变量赋值（使用助记符：putstatic）
		- 调用类的静态方法（使用助记符：invokestatic）
		- 反射（如 Class.Forname("com.test.Test"）
		- 初始化一个类的子类
		- Java 虚拟机启动时被标明为启动类的类 Java Test)
		- JDK1.7开始提供的动态语言支持java.lang.invoke.MethodHandle实例的解析结果REF\_getstatic, REF\_putstatic, REF\_invokeStatic句柄对应的类没有初始化，则初始化
	+ 被动使用
		- 除了以上七种情况，其他使用 Java 类的方式都被看作是对类的<font color="red">被动使用</font>，都不会导致类的<font color="red">初始化</font>
- 所有的Java虚拟机实现必须在每个类或接口被Java程序“<font color="red">首次主动使用</font>”时才初始化他们


### 3、类的初始化
- 对于静态字段来说，只有直接定义了该字段的类才会被初始化；
- 当一个类在初始化时，要求其父类全部都已经初始化完毕
- -XX:+TraceClassLoading，用于追踪类的加载信息并打印出来
	- -XX:+\<option>，表示开启option选项	
	- -XX:-\<option>，表示关闭option选项
	- -XX:+\<option>=\<value>，表示将option选项的值设置为value






















