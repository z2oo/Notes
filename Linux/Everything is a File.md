# Everything is a File
在Linux中，一切都是一个文件，不仅是你上传到电脑的照片、文件，你的显示器也是一个文件！  
  
## /dev目录
这些是你系统使用或可以使用的设备，在Linux中所有的东西都被认为是一个文件，所以你的硬盘会被记录为一个文件。  
  
  
## /boot目录
```
cd /boot [ENTER]
```
这会让你进入/boot目录，这就是Linux内核通常在的地方，高级用户可能因为它们自己的原因而改变内核的位置，但通常是放在这里的。你最终必须使用此目录，因为将来你可能需要使用两种或更多不同类型的内核。  
  
    
## 根目录 /root
如果你不是以`root`的身份工作，而你输入`cd /root`，你将被带到目录/root。但是，当你在那里时，你将无法做任何事情。Root的主目录是其他人的禁区。用户的主目录也受到其他用户的一定限制  
 
   
## /sbin目录
/sbin是另外一个禁止访问的目录。你可以看，但是你不能碰，这个目录就想/bin，它里面经常使用程序，但是它们只能被root用户使用。`shutdown`命令就在那里，只有root用户才能关闭这个系统。如果其他用户视图关闭系统，他会得到一个消息：只有rootyog用户能这么做  
  
  
## /tem目录
/tmp是一个用于存储临时文件的目录，如名称所示。你会发现，当你像KDE(一种运行在Linux上的桌面环境)一样使用Windows风格的系统时，这个窗口管理器将在哪里创建文件以供临时使用。当你双击照片的图标时，照片出现供你查看，但在查看照片时会创建一个临时文件。当你关闭KDE映像程序时，临时文件被删除。这通常是在windows下利用这个目录  
  
    
## /var目录
/var是可能改变其大小(即可变大变小)的某些文件的目录。例如，Linux有几个优秀的数据库，一个叫做MySQL。通常，MySQL将其数据保存在/var的子目录中/var/mysql/中。如果我有一个电子商务网站，我会有一个数据库来注册购买，这个数据库的规模将明显增长。这也是邮件服务器存储邮件的正常位置  
  
    
## /lib目录
/lib 使用于库文件的。这就是这名称的来源。程序可以使用库来执行它们的功能。不同的程序使用相同的库，所以Linux把它们存储在这里，以便每个程序知道在哪里找到它们。除非你开始受到“无法找到共享库...”等消息，否则你可能不必担心这个目录，当你下载一个程序并且不得不从源代码自己编译时，有时候会发生这种情况。  
即使如此，发生的“依赖”问题也是非常罕见的。大多数程序，即使从源代码编译，通常都有一个预配置程序，确保他们可以找到他们“依赖”运行的库。如果他们不这么做，他们会告诉你，你补鞥呢安装程序  
  
    
## /home目录
这是个存储用户个人文件的目录，我们所有人都有使用程序的一些偏好。这些首选项通常包含在也存储在用户主目录中的配置文件中。大多数文件以`.`开头  
如果你去你的主目录
```
cd /home/[username]
ls -a
```
你会看到这些配置文件  
  
    
## 其他
其他大部分版本的Linux还会提供以下这些目录
```
/mnt
/cdrom
/floppy
```
这些不应该包含任何内容。这些文件有其他作用
