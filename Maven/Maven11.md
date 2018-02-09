## Maven - 构建自动化
构建自动化定义为一种场景：一旦该工程成功构建完成，其相关的依赖工程即开始构建，目的是为了保证其依赖项目的稳定。

### 实例
考虑一个团队正在开发一个关于总线核心 Api（称其为 bus-core-api）的工程，依赖它的工程有 2 个，分别为网页 UI（称其为 app-web-ui）和应用程序桌面 UI（称其为 app-desktop-ui）。

app-web-ui 工程使用 1.0-SNAPSHOT 总线核心 Api 工程，其 POM 文件如下：

```
<project xmlns="http://maven.apache.org/POM/4.0.0" 
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>app-web-ui</groupId>
   <artifactId>app-web-ui</artifactId>
   <version>1.0</version>
   <packaging>jar</packaging>
   <dependencies>
      <dependency>
      <groupId>bus-core-api</groupId>
         <artifactId>bus-core-api</artifactId>
         <version>1.0-SNAPSHOT</version>
      </dependency>
   </dependencies>
</project>
```

app-desktop-ui 工程也正在使用 1.0-SNAPSHOT 总线核心 Api 工程，其 POM 文件如下：

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>app-desktop-ui</groupId>
   <artifactId>app-desktop-ui</artifactId>
   <version>1.0</version>
   <packaging>jar</packaging>
   <dependencies>
      <dependency>
      <groupId>bus-core-api</groupId>
         <artifactId>bus-core-api</artifactId>
         <version>1.0-SNAPSHOT</version>
      </dependency>
   </dependencies>
</project>
```

bus-core-api 工程的 POM 文件如下：

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
</project>
```
  
现在，app-web-ui 和 app-desktop-ui 工程的团队需要保证当 bus-core-api 工程有变化时他们自己相应的工程可以随时被构建。

使用快照可以保证最新的 bus-core-api 工程可以被使用，但是为了达到上述的需求，我们仍需做一些额外的工作。

我们有 2 种方式：
- 在 bus-core-api 的 pom 文件里添加一个编译目标来提醒 app-web-ui 工程和 app-desktop-ui 工程启动创建。
- 使用一个持续集成（CI）的服务器，比如 Hudson，来实现自动化创建。

### 使用 Maven
更新 bus-core-api 工程的 pom.xml 文件

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
   <build>
   <plugins>
   <plugin>
   <artifactId>maven-invoker-plugin</artifactId>
   <version>1.6</version>
      <configuration>
         <debug>true</debug>
         <pomIncludes>
            <pomInclude>app-web-ui/pom.xml</pomInclude>
            <pomInclude>app-desktop-ui/pom.xml</pomInclude> 
         </pomIncludes>
      </configuration>
      <executions>
         <execution>
            <id>build</id>
            <goals>
               <goal>run</goal>
            </goals>
         </execution>
      </executions>
   </plugin>
   </plugins>
   <build>
</project>
```

打开命令终端，进入到 C:\ > MVN > bus-core-api 的目录下，然后执行如下的 mvn 的命令。

```
C:\MVN\bus-core-api>mvn clean package -U
```
Maven 将会开始构建 bus-core-api 工程，输出日志如下：

```
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------
[INFO] Building bus-core-api
[INFO]    task-segment: [clean, package]
[INFO] ------------------------------------------------------------------
...
[INFO] [jar:jar {execution: default-jar}]
[INFO] Building jar: C:\MVN\bus-core-ui\target\
bus-core-ui-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------

```
一旦 bus-core-api 构建成功，Maven 将会自动开始构建 app-web-ui 项目， 日志如下：

```
[INFO] ------------------------------------------------------------------
[INFO] Building app-web-ui 
[INFO]    task-segment: [package]
[INFO] ------------------------------------------------------------------
...
[INFO] [jar:jar {execution: default-jar}]
[INFO] Building jar: C:\MVN\app-web-ui\target\
app-web-ui-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------
```
等到 app-web-ui 创建成功，Maven 接着开始构建 app-desktop-ui 工程，日志输出如下：

```
[INFO] ------------------------------------------------------------------
[INFO] Building app-desktop-ui 
[INFO]    task-segment: [package]
[INFO] ------------------------------------------------------------------
...
[INFO] [jar:jar {execution: default-jar}]
[INFO] Building jar: C:\MVN\app-desktop-ui\target\
app-desktop-ui-1.0-SNAPSHOT.jar
[INFO] -------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] -------------------------------------------------------------------
```

### 使用持续集成服务器（CI）
由于开发人员不需要每次多一个新的依赖工程时都去更新 bus-core-api 工程的 pom 文件， 因此使用一个持续集成的服务器更加合适，例如，添加一个新的 app-mobile-ui 的工程，它同样依赖于 bus-core-ui 工程。Hudson 将会借助 Maven 的依赖管理功能实现工程的自动化创建。  

![](http://wiki.jikexueyuan.com/project/maven/images/hudson_maven_job.jpg)  

Hunson 把每次创建工程看做一个工作。一旦工程代码合入到 svn 或者其他任何的映射到Hudson上的代码源管理工具上，Hunson 便开始一次的创建工作，等到该工作完成后，它将会自动创建其他相关的依赖工作或者依赖工程。

在上面的例子中，当 bus-core-api 的源代码在 SVN 上有更新时， Hudson 将会启动创建。当创建完成后，Hudson 开始自动寻找其依赖的工程，然后启动 app-web-ui 和 app-desktop-ui 工程。

