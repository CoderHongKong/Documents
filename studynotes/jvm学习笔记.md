# <center> jvm学习笔记 </center>

## 类的加载、链接、初始化
- ### 加载：查找并加载二进制数据
- ### 链接：
    - 验证：确保被加载的类的正确性
    - 准备：为类的<font color="red">静态变量</font>分配内存,并将其初始化为<font color="red">默认值</font>
    - 解析：<font color="red">把类中符号引用转换为直接引用</font>
- ### <font color="red">初始化：为类的静态变量赋予正确的初始值</font>