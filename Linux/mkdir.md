# mkdir命令
Linux mkdir命令是用来创建指定的名称的目录，要求创建目录的用户在当前目录中具有权限，并且指定的目录名不能是当前目录中已有的目录  
## 命令格式
```
mkdir [选项] 目录...
```
## 命令功能
通过mkdir命令可以实现在指定位置创建以DirName(指定的文件名)命名的文件夹或目录。要创建文件夹或目录的用户必须对所创建的文件夹的父文件夹具有写权限  
并且，所创建的文件夹(目录)不能与其父目录(即父文件夹)中的文件名重名，即同一个目录下不能有同名的(区分大小写)  
  
## 命令参数
```
-m, --mode=模式，设定权限<模式>(类似chmod)，而不是rwxrwxrwx-umask

-p, --parents 可以是一个路径名称。此时路径中的某些目录尚不存在，加上此选项后，系统将自动建立好那些尚不存在的目录，即一次可以建立多个目录

-v，--verbose 每次创建新目录都显示信息

--help   显示此帮助信息并退出

--version 输出版本信息并退出
```
## 命令实例
### 例一：创建一个空目录
```
mkdir test1
```
### 例二：递归创建多个目录
```
mkdir -p test2/test22
```
### 例三：创建权限为777的目录
```
mkdir -m 777 test3
```
> 什么是777权限  
777权限指的是当前用户可以对相应的文件进行读取、写入和执行的操作，我们建站的时候，有些用户没有对某个文件夹777权限，可能会造成网站报错，无法正常执行。比如：网站的缓冲文件夹，就是一定需要777权限的  

### 例四：创建新目录都显示信息
```
mkdir -v test4
```
### 例五：一个命令创建项目的目录结构
参考：[http://www.ibm.com/developerworks/cn/aix/library/au-badunixhabits.html](http://www.ibm.com/developerworks/cn/aix/library/au-badunixhabits.html)  
  
```
mkdir -vp scf/{lib/,bin/,doc/{info,product},logs/{info,product},service/deploy/{info,product}}
输出：
|-- bin
|-- doc
|   |-- info
|   `-- product
|-- lib
|-- logs
|   |-- info
|   `-- product`-- service
   	 	`-- deploy
  	    	|-- info        	`-- product
12 directories, 0 files
```

