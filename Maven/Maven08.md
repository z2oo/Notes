## Maven - 工程文档
### 创建应用程序的文档
在 C:/ MVN 目录下，创建你的 java consumerBanking 应用程序。打开 consumerBanking 文件夹并执行以下 mvn 命令
```
C:\MVN>mvn site
```
Maven 将开始构建工程。

```
[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building consumerBanking 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-site-plugin:3.3:site (default-site) @ consumerBanking ---
[WARNING] Report plugin org.apache.maven.plugins:maven-project-info-reports-plug
in has an empty version.
[WARNING]
[WARNING] It is highly recommended to fix these problems because they threaten t
he stability of your build.
[WARNING]
[WARNING] For this reason, future Maven versions might no longer support buildin
g such malformed projects.
[INFO] configuring report plugin org.apache.maven.plugins:maven-project-info-rep
orts-plugin:2.9
[INFO] Relativizing decoration links with respect to project URL: http://maven.a
pache.org
[INFO] Rendering site with org.apache.maven.skins:maven-default-skin:jar:1.0 ski
n.
[INFO] Generating "Dependencies" report    --- maven-project-info-reports-plugin
:2.9
[INFO] Generating "Dependency Convergence" report    --- maven-project-info-repo
rts-plugin:2.9
[INFO] Generating "Dependency Information" report    --- maven-project-info-repo
rts-plugin:2.9
[INFO] Generating "About" report    --- maven-project-info-reports-plugin:2.9
[INFO] Generating "Plugin Management" report    --- maven-project-info-reports-p
lugin:2.9
[INFO] Generating "Plugins" report    --- maven-project-info-reports-plugin:2.9
[INFO] Generating "Summary" report    --- maven-project-info-reports-plugin:2.9
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 10.190 s
[INFO] Finished at: 2018-02-08T14:47:43+08:00
[INFO] Final Memory: 20M/323M
[INFO] ------------------------------------------------------------------------

```
![](http://wiki.jikexueyuan.com/project/maven/images/12-site-project-structure.jpg)  

打开 C:\MVN\consumerBanking\target\site 文件夹。点击 index.html 就可以看到文档了  
![](http://wiki.jikexueyuan.com/project/maven/images/12-consumer-web-page.jpg)  

Maven 使用称作 Doxia 的文件处理引擎创建文档，它将多个源格式的文件转换为一个共通的文档模型。要编写工程文档，你可以使用以下能够被 Doxia 解析的几种常用格式来编写。

格式名称|描述|引用
--|--|--
XDoc|Maven 1.x 版本的文档格式|http://jakarta.apache.org/site/jakarta-site2.html
FML|FAQ 文档格式|http://maven.apache.org/doxia/references/fml-format.html
XHTML|可扩展 HTML 格式	|http://en.wikipedia.org/wiki/XHTML
