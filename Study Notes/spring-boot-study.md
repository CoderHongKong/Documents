# spring-boot-study
## 一、spring-boot-loader(springboot启动剖析)
### 1、spring-boot应用的打包结构
![-w369](media/15750176368166.jpg)
* BOOT-INF：项目文件
* META-INF：jar包描述文件
* org：spring自定义类加载器相关的文件（为了迎合jar包规范）

### 2、spring-boot自定义类加载器