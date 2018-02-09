## Maven - 自动化部署
一般情况下，在一个工程开发进程里，一次部署的过程包含需如下步骤：
- 合入每个子工程下的代码到 SVN 或者源代码库，并标记它。  
- 从 SVN 下载完整的源代码。
- 构建应用程序。
- 保存构建结果为 WAR 或者 EAR 类型文件并存放到一个共同的指定的网络位置上。
- 从网络上获得该文件并且部署该文件到产品线上。
- 更新文档日期和应用程序的版本号。

### 问题陈述
通常，将会有很多不同的人参与到上述部署过程中。一个团队可以负责代码的合入工作，另外一个可以负责构建，以此类推。上述的任何一个步骤都可能因为人为的原因没有被执行。例如，较旧的版本没有在网络机器上更新，负责部署的团队再一次部署了旧的版本。

### 解决方案
通过结合如下的方案来实现自动化部署：
- Maven构建和发布项目
- SubVersion、git 源代码库用以管理源代码
- 远程仓库管理工具 (Jfrog/Nexus) 用以管理工程的二进制文件。

### 更新工程的 POM.xml
我们将会使用 Maven 发布的插件来创建一个自动化发布过程：

例如： bus-core-api 工程的 POM.xml 如下
```
<project xmlns="http://maven.apache.org/POM/4.0.0" 
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>bus-core-api</groupId>
   <artifactId>bus-core-api</artifactId>
   <version>1.0-SNAPSHOT</version>
   <packaging>jar</packaging> 
   <scm>
      <url>http://www.svn.com</url>
      <connection>scm:svn:http://localhost:8080/svn/jrepo/trunk/
      Framework</connection>
      <developerConnection>scm:svn:${username}/${password}@localhost:8080:
      common_core_api:1101:code</developerConnection>
   </scm>
   <distributionManagement>
      <repository>
         <id>Core-API-Java-Release</id>
         <name>Release repository</name>
         <url>http://localhost:8081/nexus/content/repositories/
         Core-Api-Release</url>
      </repository>
   </distributionManagement>
   <build>
      <plugins>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-release-plugin</artifactId>
            <version>2.0-beta-9</version>
            <configuration>
               <useReleaseProfile>false</useReleaseProfile>
               <goals>deploy</goals>
               <scmCommentPrefix>[bus-core-api-release-checkin]-<
               /scmCommentPrefix>
            </configuration>
         </plugin>
      </plugins>
   </build>
</project>
```
在 pom.xml 里，我们常常会使用到的重要元素如下表：

元素|描述
--|--
SCM|配置 SVN 的路径，Maven 将从该路径下将代码取下来。
仓库|成功构建出来的 WAR/EAR/JAR 或者其他的构建结果存放的路径。
插件|maven-release-plugin 用以自动化部署的过程。


### Maven Release 插件
Maven 通过 maven-release-plugin 来执行如下很有用的任务：
清理工作空间，保证最新的发布进程成功进行。

```
mvn release:clean
```
回滚修改的工作空间代码和配置保证发布过程成功进行。


```
mvn release:rollback

```
```
mvn release:prepare
```
执行如下多次操作：
- 检查本地是否存在还未提交的修改
- 确保没有快照的依赖
- 改变应用程序的版本信息用以发布
- 更新 POM 文件到 SVN
- 运行测试用例
- 提交修改后的 POM 文件
- 为代码在 SVN 上做标记
- 增加版本号和附加快照以备将来发布
- 提交修改后的 POM 文件到 SVN.

 
将代码切换到之前做标记的地方，运行 Maven 部署目标来部署 WAR 文件或者构建相应的结构到仓库里。


```
mvn release:perform
```

打开命令终端，进入到 C:\ > MVN >bus-core-api 目录下，然后执行如下的 mvn 命令。
```
C:\MVN\bus-core-api>mvn release:prepare
```
Maven 开始构建整个工程。一旦构建成功即可运行如下 mvn 命令。
```
C:\MVN\bus-core-api>mvn release:perform
```
一旦构建成功，你可以验证在你仓库下上传的 JAR 文件是否生效。

