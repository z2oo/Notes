## Maven - 创建工程
Maven 使用原型（archetype）插件创建工程。要创建一个简单的 Java 应用，我们将使用 maven-archetype-quickstart 插件。在下面的例子中，我们将在 C:\MVN 文件夹下创建一个基于 maven 的 java 应用工程。

我们打开命令控制台，跳转到 C:\MVN 目录，并执行下面的 mvn 命令。
```
C:\MVN>mvn archetype:generate
```
  
```
Choose a number or apply filter (format: [groupId:]artifactId, case sensitive co
ntains): 1122:
Choose org.apache.maven.archetypes:maven-archetype-quickstart version:
1: 1.0-alpha-1
2: 1.0-alpha-2
3: 1.0-alpha-3
4: 1.0-alpha-4
5: 1.0
6: 1.1
Choose a number: 6: 6
Define value for property 'groupId': com.companyname.bank
Define value for property 'artifactId': consumerBanking
Define value for property 'version' 1.0-SNAPSHOT: :
Define value for property 'package' com.companyname.bank: :
Confirm properties configuration:
groupId: com.companyname.bank
artifactId: consumerBanking
version: 1.0-SNAPSHOT
package: com.companyname.bank
 Y: : y
```
Maven 将开始处理，并将创建完成的 java 应用工程结构。

```
[INFO] -------------------------------------------------------------------------
---
[INFO] Using following parameters for creating project from Old (1.x) Archetype:
 maven-archetype-quickstart:1.1
[INFO] -------------------------------------------------------------------------
---
[INFO] Parameter: basedir, Value: C:\Users\Asus\Desktop\maven
[INFO] Parameter: package, Value: com.companyname.bank
[INFO] Parameter: groupId, Value: com.companyname.bank
[INFO] Parameter: artifactId, Value: consumerBanking
[INFO] Parameter: packageName, Value: com.companyname.bank
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] project created from Old (1.x) Archetype in dir: C:\Users\Asus\Desktop\ma
ven\consumerBanking
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 03:24 min
[INFO] Finished at: 2018-02-08T13:45:03+08:00
[INFO] Final Memory: 15M/269M
[INFO] ------------------------------------------------------------------------
```

现在跳转到 C:/MVN 目录。有将看到一个名为 consumerBanking 的 java 应用工程（就像在 artifactId 中设定的一样）。Maven 使用一套标准的目录结构，就像这样：  
![](http://wiki.jikexueyuan.com/project/maven/images/9-project-structure.jpg)  

使用上面的例子，我们可以知道下面几个关键概念：
文件夹结构|描述
---|---
consumerBanking	|包含 src 文件夹和 pom.xml
src/main/java contains	|java 代码文件在包结构下（com/companyName/bank）。
src/main/test contains	|测试代码文件在包结构下（com/companyName/bank）。
src/main/resources	|包含了 图片 / 属性 文件（在上面的例子中，我们需要手动创建这个结构）。


Maven 也创建了一个简单的 Java 源文件和 Java 测试文件。打开 C:\MVN\consumerBanking\src\main\java\com\companyname\bank 文件夹，可以看到 App.java 文件。

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
        System.out.println( "Hello World!" );
    }
}
```
打开 C:\MVN\consumerBanking\src\test\java\com\companyname\bank 文件夹，可以看到 AppTest.java。

```
package com.companyname.bank;

import junit.framework.Test;
import junit.framework.TestCase;
import junit.framework.TestSuite;

/**
 * Unit test for simple App.
 */
public class AppTest extends TestCase 
{
    /**
     * Create the test case
     *
     * @param testName name of the test case
     */
    public AppTest( String testName )
    {
        super( testName );
    }

    /**
     * @return the suite of tests being tested
     */
    public static Test suite()
    {
        return new TestSuite( AppTest.class );
    }

    /**
     * Rigourous Test :-)
     */
    public void testApp()
    {
        assertTrue( true );
    }
}

```
开发人员需要将他们的文件按照上面表格中提到的结构放置好，接下来 Maven 将会搞定所有构建相关的复杂任务。

