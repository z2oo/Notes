## Maven - 快照
大型软件应用程序通常由多个模块组成，这是多个团队工作于同一应用程序的不同模块的常见场景。例如一个团队工作负责应用程序的前端应用用户接口工程（app-ui.jar:1.0)），同时他们使用数据服务工程（data-service.jar:1.0）。

现在负责数据服务的团队可能正在进行修正 bug 或者增强功能，并快速迭代，然后他们几乎每天都会 release 工程库文件到远程仓库中。

现在如果数据服务团队每天上传新的版本，那么就会有下面的问题：
- 每次数据服务团队发布了一版更新的代码时，都要告诉应用接口团队。
- 应用接口团队需要定期更新他们的 pom.xml 来得到更新的版本


为了解决这样的情况，快照概念发挥了作用.

### 什么是快照？
快照是一个特殊的版本，它表示当前开发的一个副本。与常规版本不同，Maven 为每一次构建从远程仓库中检出一份新的快照版本。

现在数据服务团队会将每次更新的代码的快照（例如 data-service:1.0-SNAPSHOT）发布到仓库中，来替换旧的快照 jar 文件。

### 快照 vs 版本
对于版本，Maven 一旦下载了指定的版本（例如 data-service:1.0），它将不会尝试从仓库里再次下载一个新的 1.0 版本。想要下载新的代码，数据服务版本需要被升级到 1.1。

对于快照，每次用户接口团队构建他们的项目时，Maven 将自动获取最新的快照（data-service:1.0-SNAPSHOT）。

### 应用用户接口 pom.xml
应用用户接口工程正在使用 1.0 版本的数据服务的快照

```
<project xmlns="http://maven.apache.org/POM/4.0.0" 
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
  http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>app-ui</groupId>
  <artifactId>app-ui</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>
  <name>health</name>
  <url>http://maven.apache.org</url>
  <properties>
     <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  <dependencies>
     <dependency>
     <groupId>data-service</groupId>
         <artifactId>data-service</artifactId>
         <version>1.0-SNAPSHOT</version>
         <scope>test</scope>
     </dependency>
  </dependencies>
</project>
```

### 数据服务 pom.xml
数据服务工程为每个微小的变化 release 1.0 快照

```
<project xmlns="http://maven.apache.org/POM/4.0.0" 
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
  http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>data-service</groupId>
  <artifactId>data-service</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
  <name>health</name>
  <url>http://maven.apache.org</url>
  <properties>
     <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  </project>
```
 虽然，对于快照，Maven 每次自动获取最新的快照，但你可以在任何 maven 命令中使用 -U 参数强制 maven 下载最新的快照。

```
mvn clean package -U
```
让我们打开命令控制台，进入 C:\ > MVN > app-ui 目录并执行以下 mvn 命令。
```
C:\MVN\app-ui>mvn clean package -U
```
Maven将在下载数据服务的最新快照后，开始构建工程。

