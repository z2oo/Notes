## COS
COS(Cloud Object Storage)云对象存储  

## Bucket
首先，我们来了解一下什么是Bucket  

* Bucket 原意为桶，在这里引申为存放文件的容器   
* 每个用户可以创建多个Bucket，来存放不同的容器  
* 每个用户也都可以创建、配置Bucket，来存放自己的文件。   
    
我们需要注意的是，每个 Object 都要放在一个特定的 Bucket 内，不存在一个脱离 Bucket 的 Object  

在我们的电脑上，我们使用磁盘来存储文件，在使用磁盘时，我们又划分了不同的分区。在这里，每一个Bucket 就相当于一个分区或者一个磁盘  

## Object
什么是Object  
* 在对象存储中，我们上传的文件，就变成了Object  
* 每个Object由MetaData和Data组成
* Data就是常规意义上的“文件”，即上传文件的内容
* MetaData则是文件的基础信息，比如长度、文件名、自定义的HTTP头等
* Object属于某个Bucket
  
## AppID
我们每个人可以创建很多不同的Bucket，当Bucket较多的时候就比较难以管理，这个时候，我们可以创建不同的项目，通过项目来管理不同的Bucket  

AppID就是用于区分我们在云上的不同的项目，类似于我们在程序语言中的namespace
