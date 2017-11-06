# JDBC编程步骤
## 1.加载数据库驱动。通常使用Class类的forName()方法来加载驱动：
```
Class.forName(driverClass)
```
其中driverClass就是数据库驱动类所对应的字符串，例如加载MySql的驱动：
```
Class.forName("com.mysql.jdbc.Driver");
```
而加载Oracle的驱动
```
Class.forName("oracle.jdbc.driver.OracleDriver");
```
## 2.通过DriverManager获取数据库连接。DriverManager提供了如下方法：
```
DriverManager.getConnection(String url,String user,String pass);
```
当使用DriverManager获取数据库连接时，通常需要传入三个参数：数据库URL、登录数据库用户名和密码  
数据库URL通常遵循如下写法：
```
jdbc:subprotocol:other stuff
```
上面写法中的jdbc是固定的，而subprotocol指定连接到特定数据库的驱动，而后面的other和stuff也是不固定的  
MySql数据库的URL为：
```
jdbc:mysql://hostname:3306/databasename
```
Oracle数据库的URL为：
```
jdbc:oracle:thin:@hostname:port:databasename
```
## 3.通过Connection对象创建Statement对象，Connection对象创建Statement的方法有如下三个：
- createStatement():创建基本的Statement对象
- PreparedStatement(String sql)：根据传入的SQL语句创建预编译的Statement对象
- prepareCall(Stirng sql)：根据传入的SQL语句创建CallableStatement对象
  
## 4.使用Statement执行SQL语句。所有Statement对象都有如下三个方法来执行SQL语句：
- execute()：可以执行任何SQL语句，但比较麻烦
- executeUpdate()：主要用于执行DML和DDL语句，执行DML语句返回受SQL语句影响的行数，执行DDL语句返回0
- executeQuery()：只能执行查询语句，执行后返回代表查询结果的ResultSet对象
  
## 5.操作结果集
如果执行的SQL语句是查询语句，则执行结果返回ResultSet对象，该对象保存了SQL查询语句的结果。程序可通过操纵该ResultSet对象来取出查询结果  
  
## 6.回收数据库资源
包括关闭ResultSet、Statement和Connection等资源
