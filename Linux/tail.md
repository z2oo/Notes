# tail命令
tail命令从指定点开始将文件写到标准输出，使用tail命令的-f选项可以方便地查阅正在改变的日志文件  

tail -f filename　会把filename里最尾部的内容显示在屏幕上，并且不但刷新，使你看到最新的文件内容  

## 命令格式
```
tail [必要参数][选择参数][文件]
```
## 命令功能
用于显示指定文件末尾内容，不指定文件时，作为输入内容进行处理，常用来查看日志文件

## 命令参数
```
-f，循环读取

-q，不显示处理信息

-v，显示详细的处理信息

-c <数目>，显示的字节数

-n <行数>，显示行数

-pid=PID 与-f合用，表示在进程ID，PID死掉之后结束

-q，-quiet，-silent从不输出给出文件名的首部

-s，-sleep-interval=S与-f合用，表示在每次反复的间隔休眠s秒
```
## 命令实例
### 例一：显示文件末尾内容
```
tail -n 5 log2012.log
```
显示文件最后5行内容

### 例二：循环查看文件内容
```
ping www.baidu.com > test.log &
tail -f test.log
```
ping www.baidu.com > test.log &//在后台ping远程主机，并输出文件到test.log  

这种做法也使用一个以上的档案监视  

### 例三：从第5行开始显示文件
```
tail -n +5 log2014.log
```
