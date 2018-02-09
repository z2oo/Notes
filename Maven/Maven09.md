## Maven - 工程模板
Maven 使用原型（Archetype）概念为用户提供了大量不同类型的工程模版（614 个）。Maven 使用下面的命令帮助用户快速创建 java 项目。

```
mvn archetype:generate
```

### 什么是原型？
原型是Maven插件，它的任务是根据模板创建一个项目结构。我们将使用 quickstart 原型插件创建一个简单的 java 应用程序  

### 使用工程模板
让我们打开命令控制台，跳转到 C:\ > MVN 目录并执行以下 mvn 命令
```
C:\MVN>mvn archetype:generate 
```
Maven 将开始处理，并要求选择所需的原型

```
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building Maven Stub Project (No POM) 1
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] >>> maven-archetype-plugin:3.0.1:generate (default-cli) > generate-source
s @ standalone-pom >>>
[INFO]
[INFO] <<< maven-archetype-plugin:3.0.1:generate (default-cli) < generate-source
s @ standalone-pom <<<
[INFO]
[INFO]
[INFO] --- maven-archetype-plugin:3.0.1:generate (default-cli) @ standalone-pom
---
[INFO] Generating project in Interactive mode
[WARNING] No archetype found in remote catalog. Defaulting to internal catalog
[INFO] No archetype defined. Using maven-archetype-quickstart (org.apache.maven.
archetypes:maven-archetype-quickstart:1.0)
Choose archetype:
1: internal -> org.apache.maven.archetypes:maven-archetype-archetype (An archety
pe which contains a sample archetype.)
2: internal -> org.apache.maven.archetypes:maven-archetype-j2ee-simple (An arche
type which contains a simplifed sample J2EE application.)
3: internal -> org.apache.maven.archetypes:maven-archetype-plugin (An archetype
which contains a sample Maven plugin.)
4: internal -> org.apache.maven.archetypes:maven-archetype-plugin-site (An arche
type which contains a sample Maven plugin site.
      This archetype can be layered upon an existing Maven plugin project.)
5: internal -> org.apache.maven.archetypes:maven-archetype-portlet (An archetype
 which contains a sample JSR-268 Portlet.)
6: internal -> org.apache.maven.archetypes:maven-archetype-profiles ()
7: internal -> org.apache.maven.archetypes:maven-archetype-quickstart (An archet
ype which contains a sample Maven project.)
8: internal -> org.apache.maven.archetypes:maven-archetype-site (An archetype wh
ich contains a sample Maven site which demonstrates
      some of the supported document types like APT, XDoc, and FML and demonstra
tes how
      to i18n your site. This archetype can be layered upon an existing Maven pr
oject.)
9: internal -> org.apache.maven.archetypes:maven-archetype-site-simple (An arche
type which contains a sample Maven site.)
10: internal -> org.apache.maven.archetypes:maven-archetype-webapp (An archetype
 which contains a sample Maven Webapp project.)
Choose a number or apply filter (format: [groupId:]artifactId, case sensitive co
ntains): 7:
```
按下 Enter 选择默认选项 (7：maven-archetype-quickstart)  

Maven 将询问原型的版本

```
Choose org.apache.maven.archetypes:maven-archetype-quickstart version:
1: 1.0-alpha-1
2: 1.0-alpha-2
3: 1.0-alpha-3
4: 1.0-alpha-4
5: 1.0
6: 1.1
Choose a number: 6:
```
按下 Enter 选择默认选项 (6:maven-archetype-quickstart:1.1)

Maven 将询问工程细节。按要求输入工程细节。如果要使用默认值则直接按 Enter 键。你也可以输入自己的值。

```
Define value for property 'groupId': : com.companyname.insurance
Define value for property 'artifactId': : health
Define value for property 'version': 1.0-SNAPSHOT:
Define value for property 'package': com.companyname.insurance:
```
Maven 将要求确认工程细节。按 enter 或按 Y

```
Confirm properties configuration:
groupId: com.companyname.insurance
artifactId: health
version: 1.0-SNAPSHOT
package: com.companyname.insurance
Y:
```

现在 Maven 将开始创建工程结构，显示如下:

```
[INFO] -------------------------------------------------------------------------
---
[INFO] Using following parameters for creating project from Old (1.x) Archetype:
 maven-archetype-quickstart:1.1
[INFO] -------------------------------------------------------------------------
---
[INFO] Parameter: basedir, Value: C:\Users\Asus\Desktop\maven1
[INFO] Parameter: package, Value: com.companyname.insurance
[INFO] Parameter: groupId, Value: com.companyname.insurance
[INFO] Parameter: artifactId, Value: helath
[INFO] Parameter: packageName, Value: com.companyname.insurance
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] project created from Old (1.x) Archetype in dir: C:\Users\Asus\Desktop\ma
ven1\helath
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 03:48 min
[INFO] Finished at: 2018-02-08T15:00:18+08:00
[INFO] Final Memory: 16M/252M
[INFO] ------------------------------------------------------------------------
```

### 创建的项目
现在转到 C:\ > MVN 目录。你会看到一个名为 health 的 java 应用程序项目，就像在项目创建的时候建立的 artifactId 名称一样。 Maven 将创建一个有标准目录布局的工程，如下所示:  
![](http://wiki.jikexueyuan.com/project/maven/images/13-template_project_structure.jpg)  
  
  
### 创建 POM.xml
Maven 为工程生成一个 POM.xml文件，如下所列:
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.companyname.insurance</groupId>
  <artifactId>helath</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>helath</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>

```

### 创建 App.java
Maven 生成一个 java 源文件示例，工程的 App.java 如下所示:

路径：C:\ > MVN > health > src > main > java > com > companyname > insurance > App.java


### 创建 AppTest.java
Maven 生成一个 java 源文件示例，工程的 AppTest.java 如下所示：

路径为： C:\ > MVN > health > src > test > java > com > companyname > insurance > AppTest.java

可以用 maven 简单的命令创建任何类型的工程，并且可以启动您的开发