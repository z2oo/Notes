如果用户需要创建新的数据库，则使用
```
create database [IF NOT EXISTS] 数据库名;
```
如果用户需要删除指定数据库，则可以使用如下命令：
```
drop database 数据库名;
```
建立数据库后，如果想操作该数据库，则需要进入该数据库：
```
use 数据库名;
```
进入指定数据库后，如果需要查询该数据库下包含多少个数据表，则：
```
show tables;
```
如果想查看指定数据表的表结构（查看该表有多少列，每列的数据类型信息），使用：
```
desc 表名;
```
  
  
  
如果将多条MySql命令写在一份SQL脚本文件里，然后将这份SQL脚本的内容一次复制到该窗口里，将可以看到该命令行客户端一次性执行所有SQL命令的效果--这种一次性执行多条SQL命令的方式也被称为导入SQL脚本。  

MySQL数据库安装成功后，在其安装目录下有一个bin路径，该路径下包含一个mysql命令，该命令用于启动MySQL命令行客户端。执行mysql命令的语法如下：
```
mysql -p 密码 -u 用户名 -h 主机名 --default-character-set=utf8
```
执行上面命令可以连接远程主机的MySQL服务。为了保证有较好的安全性，执行上面命令时可以省略-p后面的密码，执行该命令后系统会提示输入密码。
  
  
 
SQL的全称是Structured Query Language，也就是结构化查询语言。SQL是操作和检索关系数据库的标准语言，标准的SQL语句可用于操作任何关系数据库。
  
  
使用SQL语句，程序员和数据库管理员（DBA）可以完成如下任务：
- 在数据库中检索信息。
- 对数据库的信息进行更新。
- 改变数据库的结构。
- 更改系统的安全设置。
- 增加或回收用户对数据库、表的许可权限。
在上面5个任务中，一般程序员可以管理前3个任务，后面2个任务通常有DBA来完成。  

标准的SQL语句通常可分为如下几种类型：
- 查询语句：主要有select关键字完成，查询语句是SQL语句中最复杂、功能最丰富的语句。
- DML（Data Manipulation Language，数据操作语言）语句：主要由insert、update和delete三个关键字完成。
- DDL（Data Definition Language,数据定义语言）语句：主要由create、alter、alter、drop和truncate四个关键字完成。
- DCL（Data Control Language,数据控制语言）语句：主要有grant和revoke两个关键字完成。
- 事务控制语句：主要有commit、rollback和savepoint三个关键字完成。  

SQL语句的关键字不区分大小写，也就是说，create和CREATE的作用完全一样。  


在SQL命令中也可能需要使用标识符，标识符可用于定义表名、列名，也可用于定义变量等。规则如下：
- 标识符通常必须以字母开头。
- 标识符包括字母、数字和三个特殊字符（#_$）。
- 不要使用单钱数据库系统的关键字、保留字，通常建议使用多个单词连缀而成，单词之间以_分割。
- 同一个模式下的对象不应该同名，这里的模式指的是外模式。



## DDL语句
DDL语句是操作数据库对象的语句，包括创建（create）、删除（drop）和修改（alter）数据库对象。  
最基本的数据库对象是数据表，数据表是存储数据的逻辑单元。但数据库里绝不仅包括数据表，数据库里可包含如下几种常见的数据库对象。

对象名称 | 对应关键字|描述
---|---|---
表 | table |表是存储数据的逻辑单元，以行和列的形式存在；列就是字段，行就是记录
数据字典 |  |就是系统表，存放数据库相关信息的表。系统表里的数据通常由数据库系统维护，程序员通常不应该手动修改系统表及系统表数据，止咳查看系统表数据
约束 | constraint |执行数据校验的规则，用于保证数据完整性的规则
视图 | view | 一个或者多个数据表里数据的逻辑显示。视图并不存储数据
索引 |index | 用于提高查询性能，相当于书的目录
函数 |function | 用于完成一次特定的计算，具有一个返回值
存储过程 |procedure | 用于完成一次完整的业务处理，没有返回值，但可通过传出参数将多个值传给调用环境
触发器 | trigger | 相当于一个时间监听器，当数据库发生特定时间后，触发器被触发，完成相应的处理

因为存在上面几种数据库对象，所以create后可以紧跟不同的关键字。例如，建表应使用create table，建索引应使用create index，建视图应使用create view······drop和alter后也需要添加类似的关键字来表示删除、修改哪种数据库对象。  

### 创建表的语法
标准的建表语句的语法如下：
```
create table [模式名.]表名
(
    #可以有多个列定义
    columnName1 datatype [default expr],
    ...
)
```
上面语法中圆括号里可以包含多个列定义，每个列定义之间以英文逗号（,）隔开，最后一个列定义不需要使用英文逗号，而是直接以括号结束。  
前面已经讲过，建立数据表只是建立表结构，就是指定该数据表有多少列，每列的数据类型，所以建表乬的重点就是圆括号里的列定义，列定义由列名、列类型和可选的默认值组成。  
列定义有点类似java里的变量定义，与变量定义不同的是，列定义时将列名放在前面，列类型放在后面。如果要指定列的默认值，则使用default关键字，而不是使用等号（=）。  
例如下面的建表语句：
```
create table test
(
    #整型通常用int
    test_id int,
    #小数点数
    test_price decimal,
    #普通长度文本，使用default指定默认值
    test_name varchar(255) default 'xxx'
    #大文本类型
    test_desc text,
    #图片
    test_img blob,
    test_date datetime
);
```
建表时需要指定每列的数据类型，不同数据库所支持的列类型不同，这需要查阅不同数据库的相关文档。  
#### MySQL支持的列类型
列类型| 说明
tinyint/smallint/mediumint/int(integer)/bigint  | 1字节/2字节/3字节/4字节/8字节整数，又可分为有符号和无符号两种。这些整数类型的区别仅仅是表述范围不同
float/double | 单精度、双精度浮点类型
decimal(dec) | 精确小数类型，相对于float和double不会产生精度丢失的问题
date | 日期类型，不能保存时间。把java.util.Date对象保存进date列时，时间部分将会丢失
time | 时间类型，不能保存日期。
datetime | 日期、时间类型
timestamp | 时间戳类型
year | 年类型，仅仅保存时间的年份
char | 定长字符串类型
varchar | 可变长度字符串类型
binary | 定长二进制字符串类型，它以二进制形式保存字符串
varbinary | 可变长度的二进制字符串类型，它以二进制形式保存字符串
tinyblob/blob/mediumblob/longblob | 1字节/2字节/3字节/4字节的二进制大对象，可用于存储图片、音乐等二进制数据，分别可存储：255B/64KB/16MB/4GB的大小
tinytext/text/mediumtext/longtext | 1字节/2字节/3字节/4字节的文本对象，可用于存储超长长度的字符串，分别可存储：255B/64KB/16MB/4GB的大小
enum('value1','value2',..) |枚举类型，该列的值只能是enum后括号里多个值的其中之一
set('value1','value2',..) | 集合类型，该列的值可以是set后括号里的多个值的其中几个
上面是比较常见的建表语句，这种建表语句只是创建一个空表，该空表里没有任何数据。如果使用子查询建表语句，则可以在建表的同时插入数据。子查询建表语句的语法如下：
```
create table [模式名.]表名 [column[,column...]]
as subquery;
```
上面语法中新表的字段列表必须与子查询中的字段列表数量匹配，创建新表时的字段列表可以省略，如果省略了该字段列表，则新表的列名与选择结果完全相同。下面语句使用子查询来建表。
```
#创建hehe数据表，该数据表和user_inf完全相同，数据也完全相同
create table hehe
as 
select * from user_inf;
```
因为上面语句是利用子查询来建立数据表，所以执行该SQl语句要求数据库中已存在user_inf数据表，否则程序将出现错误。  

> 提示
当数据表创建成功后，MySQL使用information_schema数据库里的TABLES表来保存该数据库实例中的所有数据表，用户可通过查询TABLES表来获取该数据库的表信息。


### 修改表结构的语法
修改表结构使用alter table，修改表结构包括增加列定义、修改列定义、删除列、重命名列等操作。  
增加列定义的语法如下：
```
alter table 表名
add
(
    #可以有多个列定义
    column_name1 datatype [default expr],
    ...
);
```
上面的语法格式中圆括号部分与建表语法的圆括号部分完全相同，只是此时圆括号里的列定义是追加到已有表的列定义后面。还有一点需要指出，如果只是新增一列，则可以省略圆括号，仅在add后紧跟一个列定义即可。为数据表增加字段的SQL语句如下：
```
#为hehe数据表增加一个hehe_id字段，该字段的类型为int
alter table hehe
add hehe_id int;
#为hehe数据表增加aaa、bbb字段，两个字段的类型都为varchar(255)
alter table hehe
add
(
    aaa varchar(255) default 'xxx'
    bbb varchar(255) 
);
```
上面第二条SQL语句增加aaa字段时，为该字段指定默认值为'xxx'。值得指出的是，SQL语句中的字符串值不是用双引号引起，而是用单引号引起的。  
增加字段时，需要注意:如果数据表中已有数据记录，除非给新增的列指定了默认值，否则新增的数据列不可指定非空约束，因为那些已有的记录在新增列上肯定是空（实际上，修改表结构很容易失败，只要新增的约束与已有数据冲突，修改就会失败）。  
修改列定义的语法如下：
```
alter table 表名
modify column_name datatype [default expr] [first|after col_name];
```
上面语法中`first`或者`after col_name`指定需要将目标修改到指定位置。  
从上面修改语法中可以看出，该修改语句每次只能修改一个列定义，如下代码所示：  
```
#将hehe表的hehe_id列修改成varchar(255)类型
alter table hehe
modify hehe_id varchar(255);
#将hehe表的bbb列修改成int类型
alter table hehe
modify bbb int;
```
从上面代码中不难看出，使用SQL修改数据表里列定义的语法和为数据表只增加一个列定义的语法几乎完全一样，关键是增加列定义使用add关键字，而修改列定义使用modify关键字。还有一点需要指出，add新增的列名必须是原表中不存在的，而modify修改的列名必须是原表中已存在的。  

> 注意：
虽然MySQL的一个modify命令不支持一次修改多个列定义，但其他数据库入Oracle支持一个modify命令修改多个列定义，一个modify命令修改多个列定义的语法和一个add命令增加多个列定义的语法非常相似，也需要使用圆括号把多个列定义括起来。如果需要让MySQL支持一次修改多个列定义，则可在alter table后使用多个modify命令。  

如果数据表里已有数据记录，则修改列定义非常失败，因为有可能修改的列定义规则与原有的数据记录不符合。如果修改数据列的默认值，则只会对以后的插入操作有用，对以前已经存在的数据不会有任何以影响。  
  
从数据表中删除列的语法比较简单：
```
alter table 表名
drop column_name
```
删除列只要在drop后紧跟需要删除的列名即可。例如：
```
删除hehe表中的aaa字段
alter table hehe
drop aaa;
```
从数据表中删除列定义通常总是可以成功，删除列定义时将从每行中删除该列的数据，并释放该列在数据块中占用的空间。所以删除大表中的字段时需要比较长的时间，因为还需要回收空间。  
上面介绍的这些增加列、修改列和删除列的语法是标准的SQL语法，对所有的数据库都通用。除此之外，MySQL还提供了两种特殊的语法：重命名数据表和完全改变列定义。  
重命名数据表的语法格式如下：  
```
alter table 表名
rename to 新表名
```
如下SQL语句用于将hehe表命名为wawa：
```
#将hehe数据表重命名为wawa
alter table hehe
rename to wawa;
```
MySQL为alter table提供了change选项，该选项可以改变列名。change选项的语法如下：
```
alter table 表名
change old_column_name new_column_name type [default expr] [first|after col_name]
```
对比change和modify两个选项，不难发现：change选项比modify选项多了一个列名，因为change选项可以改变列名，所以它需要两个列名。一般而言，如果不需要改变列名，使用`alter table`的`modify`选项即可，只有当需要修改列名时才会使用change选项。如下语句所示：
```
#将wawa数据表的bbb字段重命名为ddd
alter table wawa
change bbb ddd int;
```
### 删除表的语法
删除表的语法格式如下：
```
drop table 表名;
```
如下SQL语句将会把数据库中已有的wawa数据表删除：
```
#删除数据表
drop table wawa;
```
删除数据表的效果如下。
- 表结构被删除，表对象不再存在。
- 表里的所有数据也被删除。
- 该表所有相关的索引、约束也被删除。
  
### truncate表
对于大部分数据库而言，truncate都被当成DDL处理，truncate被称为“截断”某个表———它的作用是删除该表里的全部数据，但保留表结构。相对于DML里的delte命令而言，truncate的速度要快得多，而且truncate不像delete可以删除指定的记录，truncate只能一次性删除整个表的全部记录。truncate命令的语法如下：
```
truncate 表名
```


## 数据库约束
大部分数据库支持5中完整性约束
- NOT NULL:非空约束，指定某列不能为空。
- UNIQUE:唯一约束，指定某列或者几列组合不能重复
- PRIMARY KEY：主键，指定该列的值可以唯一地标志该条记录。
- FOREIGN KEY:外检，指定该行记录从属于主表中的一条记录，主要用于保证参照完整性。
- CHECK:检查，指定一个布尔表达式，用于指定对应列的值必须满足该表达式。
