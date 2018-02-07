## Maven - 构建生命周期
### 什么是构建生命周期
构建生命周期是一组阶段的序列（sequence of phases），每个阶段定义了目标被执行的顺序。这里的阶段是生命周期的一部分。

举例说明，一个典型的Maven构建生命周期是由以下几个阶段的序列组成的：
阶段|处理|描述
--|--|--
prepare-resources|资源拷贝|本阶段可以自定义需要拷贝的资源
compile|编译|本阶段完成源代码编译
package|打包|本阶段根据 pom.xml 中描述的打包配置创建 JAR / WAR 包
install|安装|本阶段在本地 / 远程仓库中安装工程包

当需要在某个特定阶段之前或之后执行目标时，可以使用 pre 和 post 来定义这个目标。

当 Maven 开始构建工程，会按照所定义的阶段序列的顺序执行每个阶段注册的目标。Maven有以下三个标准的生命周期：
- clean
- default(or build)
- site

目标表示一个特定的、对构建和管理工程有帮助的任务。它可能绑定了 0 个或多个构建阶段。没有绑定任何构建阶段的目标可以在构建生命周期之外被直接调用执行。

执行的顺序依赖于目标和构建阶段被调用的顺序。例如，考虑下面的命令。clean 和 package 参数是构建阶段，而 dependency:copy-dependencies 是一个目标。
```
mvn clean dependency:copy-dependencies package
```
这里的 clean 阶段将会被首先执行，然后 dependency:copy-dependencies 目标会被执行，最终 package 阶段被执行。

### Clean 生命周期
当我们执行 mvn post-clean 命令时，Maven 调用 clean 生命周期，它包含以下阶段。
- pre-clean
- clean
- post-clean
 
Maven 的 clean 目标（clean:clean）绑定到了 clean 生命周期的 clean 阶段。它的 clean:clean 目标通过删除构建目录删除了构建输出。所以当 mvn clean 命令执行时，Maven 删除了构建目录。

我们可以通过在上面的clean生命周期的任何阶段定义目标来修改这部分的操作行为。

在下面的例子中，我们将 maven-antrun-plugin:run 目标添加到 pre-clean、clean 和 post-clean 阶段中。这样我们可以在 clean 生命周期的各个阶段显示文本信息。

创建了一个 pom.xml 文件。  
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.companyname.projectgroup</groupId>
<artifactId>project</artifactId>
<version>1.0</version>
<build>
<plugins>
   <plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-antrun-plugin</artifactId>
   <version>1.1</version>
   <executions>
      <execution>
         <id>id.pre-clean</id>
         <phase>pre-clean</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>pre-clean phase</echo>
            </tasks>
         </configuration>
      </execution>
      <execution>
         <id>id.clean</id>
         <phase>clean</phase>
         <goals>
          <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>clean phase</echo>
            </tasks>
         </configuration>
      </execution>
      <execution>
         <id>id.post-clean</id>
         <phase>post-clean</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>post-clean phase</echo>
            </tasks>
         </configuration>
      </execution>
   </executions>
   </plugin>
</plugins>
</build>
</project>
```
执行下面的 mvn 命令  
```
mvn post-clean
```
Maven 将会开始处理并显示 clean 生命周期的所有阶段  
```
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building project 1.0
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-antrun-plugin:1.1:run (id.pre-clean) @ project ---
[INFO] Executing tasks
     [echo] pre-clean phase
[INFO] Executed tasks
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ project ---
[INFO]
[INFO] --- maven-antrun-plugin:1.1:run (id.clean) @ project ---
[INFO] Executing tasks
     [echo] clean phase
[INFO] Executed tasks
[INFO]
[INFO] --- maven-antrun-plugin:1.1:run (id.post-clean) @ project ---
[INFO] Executing tasks
     [echo] post-clean phase
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.708 s
[INFO] Finished at: 2018-02-07T17:01:59+08:00
[INFO] Final Memory: 8M/245M
[INFO] ------------------------------------------------------------------------
```
可以尝试修改 mvn clean 命令，来显示 pre-clean 和 clean，而在 post-clean 阶段不执行任何操作  

### Default (or Build) 生命周期
这是 Maven 的主要生命周期，被用于构建应用。包括下面的 23 个阶段  

生命周期阶段|描述
--|--
validate|检查工程配置是否正确，完成构建过程的所有必要信息是否能够获取到。
initialize|初始化构建状态，例如设置属性。
generate-sources|生成编译阶段需要包含的任何源码文件。
process-sources	|处理源代码，例如，过滤任何值（filter any value）。
generate-resources	|生成工程包中需要包含的资源文件。
process-resources|拷贝和处理资源文件到目的目录中，为打包阶段做准备。
compile	|编译工程源码。
process-classes	|处理编译生成的文件，例如 Java Class 字节码的加强和优化。
generate-test-sources|生成编译阶段需要包含的任何测试源代码。
process-test-sources|处理测试源代码，例如，过滤任何值（filter any values)。
test-compile|编译测试源代码到测试目的目录。
process-test-classes|处理测试代码文件编译后生成的文件。
test|使用适当的单元测试框架（例如JUnit）运行测试。
prepare-package	|在真正打包之前，为准备打包执行任何必要的操作。
package	|获取编译后的代码，并按照可发布的格式进行打包，例如 JAR、WAR 或者 EAR 文件。
pre-integration-test|在集成测试执行之前，执行所需的操作。例如，设置所需的环境变量。
integration-test|处理和部署必须的工程包到集成测试能够运行的环境中。
post-integration-test|在集成测试被执行后执行必要的操作。例如，清理环境。
verify|运行检查操作来验证工程包是有效的，并满足质量要求。
install	|安装工程包到本地仓库中，该仓库可以作为本地其他工程的依赖。
deploy	|拷贝最终的工程包到远程仓库中，以共享给其他开发人员和工程。

有一些与 Maven 生命周期相关的重要概念需要说明：

当一个阶段通过 Maven 命令调用时，例如 mvn compile，只有该阶段之前以及包括该阶段在内的所有阶段会被执行。

不同的 maven 目标将根据打包的类型（JAR / WAR / EAR），被绑定到不同的 Maven 生命周期阶段。

在下面的例子中，我们将 maven-antrun-plugin:run 目标添加到 Build 生命周期的一部分阶段中。这样我们可以显示生命周期的文本信息。

 pom.xml 文件内容
 ```
 <project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
  http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.companyname.projectgroup</groupId>
<artifactId>project</artifactId>
<version>1.0</version>
<build>
<plugins>
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-antrun-plugin</artifactId>
<version>1.1</version>
<executions>
   <execution>
      <id>id.validate</id>
      <phase>validate</phase>
      <goals>
         <goal>run</goal>
      </goals>
      <configuration>
         <tasks>
            <echo>validate phase</echo>
         </tasks>
      </configuration>
   </execution>
   <execution>
      <id>id.compile</id>
      <phase>compile</phase>
      <goals>
         <goal>run</goal>
      </goals>
      <configuration>
         <tasks>
            <echo>compile phase</echo>
         </tasks>
      </configuration>
   </execution>
   <execution>
      <id>id.test</id>
      <phase>test</phase>
      <goals>
         <goal>run</goal>
      </goals>
      <configuration>
         <tasks>
            <echo>test phase</echo>
         </tasks>
      </configuration>
   </execution>
   <execution>
         <id>id.package</id>
         <phase>package</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
         <tasks>
            <echo>package phase</echo>
         </tasks>
      </configuration>
   </execution>
   <execution>
      <id>id.deploy</id>
      <phase>deploy</phase>
      <goals>
         <goal>run</goal>
      </goals>
      <configuration>
      <tasks>
         <echo>deploy phase</echo>
      </tasks>
      </configuration>
   </execution>
</executions>
</plugin>
</plugins>
</build>
</project>
```

现在打开命令控制台，跳转到pom.xml所在目录，并执行以下 mvn 命令。
```
mvn compile
```
Maven 将会开始处理并显示直到编译阶段的构建生命周期的各个阶段。
```
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building project 1.0
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-antrun-plugin:1.1:run (id.validate) @ project ---
[INFO] Executing tasks
     [echo] validate phase
[INFO] Executed tasks
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ project --
-
[WARNING] Using platform encoding (GBK actually) to copy filtered resources, i.e
. build is platform dependent!
[INFO] skip non existing resourceDirectory C:\Users\Asus\Desktop\maven\src\main\
resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ project ---
[INFO] No sources to compile
[INFO]
[INFO] --- maven-antrun-plugin:1.1:run (id.compile) @ project ---
[INFO] Executing tasks
     [echo] compile phase
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.508 s
[INFO] Finished at: 2018-02-07T17:51:32+08:00
[INFO] Final Memory: 8M/245M
[INFO] ------------------------------------------------------------------------

```



### Site 生命周期
Maven Site 插件一般用来创建新的报告文档、部署站点等。

阶段：
- pre-site
- site
- post-site
- site-deploy

在下面的例子中，我们将 maven-antrun-plugin:run 目标添加到 Site 生命周期的所有阶段中。这样我们可以显示生命周期的所有文本信息。

pom.xml 文件内容
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
  http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.companyname.projectgroup</groupId>
<artifactId>project</artifactId>
<version>1.0</version>
<build>
<plugins>
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-antrun-plugin</artifactId>
<version>1.1</version>
   <executions>
      <execution>
         <id>id.pre-site</id>
         <phase>pre-site</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>pre-site phase</echo>
            </tasks>
         </configuration>
      </execution>
      <execution>
         <id>id.site</id>
         <phase>site</phase>
         <goals>
         <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>site phase</echo>
            </tasks>
         </configuration>
      </execution>
      <execution>
         <id>id.post-site</id>
         <phase>post-site</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>post-site phase</echo>
            </tasks>
         </configuration>
      </execution>
      <execution>
         <id>id.site-deploy</id>
         <phase>site-deploy</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>site-deploy phase</echo>
            </tasks>
         </configuration>
      </execution>
   </executions>
</plugin>
</plugins>
</build>
</project>
```
跳转到 pom.xml 所在目录，并执行以下 mvn 命令
```
mvn site
```
Maven 将会开始处理并显示直到 site 阶段的 site 生命周期的各个阶段。

```[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building project 1.0
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-antrun-plugin:1.1:run (id.pre-site) @ project ---
[INFO] Executing tasks
     [echo] pre-site phase
[INFO] Executed tasks
[INFO]
[INFO] --- maven-site-plugin:3.3:site (default-site) @ project ---
[INFO] configuring report plugin org.apache.maven.plugins:maven-project-info-rep
orts-plugin:2.9
[INFO] Rendering site with org.apache.maven.skins:maven-default-skin:jar:1.0 ski
n.
[INFO] Generating "Dependency Convergence" report    --- maven-project-info-repo
rts-plugin:2.9
[INFO] Generating "Dependency Information" report    --- maven-project-info-repo
rts-plugin:2.9
[INFO] Generating "About" report    --- maven-project-info-reports-plugin:2.9
[INFO] Generating "Plugin Management" report    --- maven-project-info-reports-p
lugin:2.9
[INFO] Generating "Plugins" report    --- maven-project-info-reports-plugin:2.9
[INFO] Generating "Summary" report    --- maven-project-info-reports-plugin:2.9
[INFO]
[INFO] --- maven-antrun-plugin:1.1:run (id.site) @ project ---
[INFO] Executing tasks
     [echo] site phase
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 5.237 s
[INFO] Finished at: 2018-02-07T18:01:11+08:00
[INFO] Final Memory: 18M/324M
[INFO] ------------------------------------------------------------------------

```