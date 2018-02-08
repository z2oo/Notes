## Maven - 构建 & 测试工程
跳转到 C:/MVN 目录下，既你的 java 应用目录下。打开 consumerBanking 文件夹。你将看到 POM.xml 文件中有下面的内容：  
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <groupId>com.companyname.projectgroup</groupId>
      <artifactId>project</artifactId>
      <version>1.0</version>
      <dependencies>
         <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
         </dependency>
      </dependencies>  
</project>
```
可以看到，Maven 已经添加了 JUnit 作为测试框架。默认 Maven 添加了一个源码文件 App.java 和一个测试文件 AppTest.java 到上个章节中我们提到的默认目录结构中。

打开命令控制台，跳转到 C:\MVN\consumerBanking 目录下，并执行以下 mvn 命令。

```
C:\MVN\consumerBanking>mvn clean
```  
```
C:\MVN\consumerBanking>mvn  package
```
Maven 将开始构建工程。


你已经构建了你的工程并创建了最终的 jar 文件，下面是要学习的关键概念：
- 我们给了 maven 两个目标，首先清理目标目录（clean），然后打包工程构建的输出为 jar（package）文件。
- 打包好的 jar 文件可以在 consumerBanking\target 中获得，名称为 consumerBanking-1.0-SNAPSHOT.jar。
- 测试报告存放在 consumerBanking\target\surefire-reports 文件夹中。
- Maven 编译源码文件，以及测试源码文件。
- 接着 Maven 运行测试用例。
- 最后 Maven 创建工程包。

现在打开命令控制台，跳转到 C:\MVN\consumerBanking\target\classes 目录，并执行下面的 java 命令。

```
C:\MVN\consumerBanking\target\classes>java com.companyname.bank.App
```
你可以看到结果：

```
Hello World!
```

### 添加 Java 源文件
我们看看如何添加其他的 Java 文件到工程中。打开 C:\MVN\consumerBanking\src\main\java\com\companyname\bank 文件夹，在其中创建 Util 类 Util.java。

```
package com.companyname.bank;

public class Util 
{
   public static void printMessage(String message){
       System.out.println(message);
   }
}
```
更新 App 类来使用 Util 类。

```
package com.companyname.bank;

/**
 * Hello world!
 *
 */
public class App 
{
    public static void main( String[] args )
    {
        Util.printMessage("Hello World!");
    }
}
```
现在打开命令控制台，跳转到 C:\MVN\consumerBanking 目录下，并执行下面的 mvn 命令。

```
mvn clean
```
```
mvn compile
```
在 Maven 构建成功之后，跳转到 C:\MVN\consumerBanking\target\classes 目录下，并执行下面的 java 命令。

```
C:\MVN\consumerBanking\target\classes>java -cp com.companyname.bank.App

```
你可以看到结果：

```
Hello World!
```