## Maven是什么
Maven 是一个项目管理和整合工具。Maven为开发者提供了一套完整的构建生命周期框架。开发团队几乎不用花多少时间就能够自动完成工程的基础构建配置，因为Maven使用了一个标准的目录结构和一个默认的构建生命周期。  

在有多个开发团队环境的情况下，Maven能够在很短的时间内使得每项工作都按照标准进行。因为大部分的工程配置操作都非常简单并且可复用，在创建报告、检查、构建和测试自动配置时，Maven 可以让开发者的工作变得更简单。

  
  
Maven 能够帮助开发者完成以下工作：
- 构建
- 文档生成
- 报告
- 依赖
- SCMs
- 发布
- 分发
- 邮件列表

总的来说，Maven简化了工程的构建过程，并对其标准化。它无缝衔接了编译、发布、文档生成、团队合作和其他任务。Maven 提高了重用性，负责了大部分构建相关的任务。  

## Maven 的目标
Maven 的主要目的是为开发者提供  
- 一个可复用、可维护、更易理解的工程综合模型
- 与这个模型交互的插件或者工具

Maven 工程结构和内容被定义在一个xml文件中－pom.xml，是 Project Object Model(POM)的简称，此文件是整个Maven系统的基础组件。  
## 约定优于配置
Maven 使用约定而不是配置，意味着开发者不需要再自己创建构建过程。

Maven 使用约定而不是配置，意味着开发者不需要再自己创建构建过程。

举例说明，下面的表格展示了工程源码文件、资源文件的默认配置，和其他一些配置。假定 ${basedir} 表示工程目录：

配置项|默认值
--|--
source code|${basedir}/src/main/java
resources | ${basedir}/src/main/resources
Tests | ${basedir}/src/test
Complied byte code	| ${basedir}/target
distributable JAR	| ${basedir}/target/classes
  
  
为了构建工程，Maven为开发者提供了选项来配置生命周期目标和工程依赖（依赖于Maven的插件扩展功能和默认的约定）。大部分的工程管理和构建相关的任务是由 Maven 插件完成的。  

开发人员不需要了解每个插件是如何工作的，就能够构建任何给定的 Maven 工程  


## Maven - 环境配置
Maven 是一个基于Java的工具，所以要做的第一件事情就是安装 JDK。  

1. 步骤 1：检查 Java 安装
2. 步骤 2：设置 Java 环境
3. 步骤 3：下载 Maven 文件
4. 步骤 4：解压 Maven 文件
    - 解压文件到你想要的位置来安装 Maven 3.2.5，你会得到 apache-maven-3.2.5 子目录。
5. 步骤 5：设置 Maven 环境变量
6. 步骤 6：添加 Maven bin 目录到系统路径中
7. 步骤 7：验证 Maven 安装

## Maven - POM
POM 代表工程对象模型。它是使用Maven工作时的基本组建，是一个 xml文件。它被放在工程根目录下，文件命名为 pom.xml。  

POM 包含了关于工程和各种配置细节的信息，Maven 使用这些信息构建工程。  

POM 也包含了目标和插件。当执行一个任务或者目标时，Maven 会查找当前目录下的POM，从其中读取所需要的配置信息，然后执行目标。  

能够在 POM 中设置的一些配置如下：
- project dependencies
- plugins
- goals
- build profiles
- project version
- developers
- mailing list

在创建 POM 之前，我们首先确定工程组（groupId），及其名称（artifactId）和版本，在仓库中这些属性是工程的唯一标识。

### POM 举例  
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>

   <groupId>com.companyname.project-group</groupId>
   <artifactId>project</artifactId>
   <version>1.0</version>

</project>
```
需要说明的是每个工程应该只有一个 POM 文件。  
- 所有的 POM 文件需要 project 元素和三个必须的字段：groupId, artifactId,version。
- 在仓库中的工程标识为 groupId:artifactId:version
- POM.xml 的根元素是 project，它有三个主要的子节点：
 
节点|描述
--|--  
groupId|这是工程组的标识。它在一个组织或者项目中通常是唯一的。例如，一个银行组织 com.company.bank 拥有所有的和银行相关的项目。
artifactId|这是工程的标识。它通常是工程的名称。例如，消费者银行。groupId 和 artifactId 一起定义了 artifact 在仓库中的位置。
version|这是工程的版本号。在 artifact 的仓库中，它用来区分不同的版本。例如：com.company.bank:consumer-banking:1.0                    .company.bank:consumer-banking:1.1.


### Super POM
所有的 POM 都继承自一个父POM（无论是否显式定义了这个父 POM）。父POM也被称作SuperPOM，它包含了一些可以被继承的默认设置  

Maven 使用 effective pom（Super pom 加上工程自己的配置）来执行相关的目标，它帮助开发者在 pom.xml 中做尽可能少的配置，当然这些配置可以被方便的重写。

查看 Super POM 默认配置的一个简单方法是执行以下命令：mvn help:effective-pom

在你的电脑上的任意目录下创建一个 pom.xml 文件，使用上面提到的示例 pom 中的内容。

Maven 的 pom.xml 文件也不需要手工编写。

Maven 提供了大量的原型插件来创建工程，包括工程结构和 pom.xml。






















  
  


