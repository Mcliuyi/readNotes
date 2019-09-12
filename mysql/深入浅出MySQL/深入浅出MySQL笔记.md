

# 深入浅出MySQL(第二版)

## 1. SQL分类

- **DDL**

  数据库定义语言

  ```mysql
  create, drop, alter
  ```

  ```mysql
  CREATE DATABASE `mydb` CHARACTER SET utf8 COLLATE utf8_general_ci;
  # 创建数据库是设置utf-8编码
  ```

  

- **DML**

  数据操作语言

  ```mysql
  insert into, select...
  ```

- DCL

  数据控制语句

  用于权限管理等语句

  ```mysql
  grant, revoke
  ```



## 2. 使用帮助命令

通过`? func`可以查看对应函数或者变量的解释与帮助

如: `? data types`查看所有的支持数据类型

```mysql
mysql> ? data types
You asked for help about help category: "Data Types"
For more information, type 'help <item>', where <item> is one of the following
topics:
   AUTO_INCREMENT
   BIGINT
   BINARY
   BIT
   BLOB
   BLOB DATA TYPE
   BOOLEAN
   CHAR
   CHAR BYTE
   DATE
	 ....
```

## 2.1 查询元数据

在mysql中存在一个默认的数据库`information_schema`，里面存放了大量的视图

是一个虚拟数据库。

可以通过下面类似命令统一对多个数据库进行操作

```mysql
select concat('drop table test.', table_name,';') from tables where table_schema='test' and table_name like 'tmp%'; # 删除test数据库下，所有以tmp为前缀的表
select concat('alter table test.', table_name,'engine=innodb;') from tables where table_schema='test' and engine="MyISAM" # test数据库下，将所有的MyISAM引擎转换为Innodb引擎
```

- **SCHEMATA**

  保存了当前mysql实例中所有的数据库信息，`show databases;`结果来自此表

- **TABLES**

  包含了数据库中所有表(包含视图)的信息，详细的记录了表属于个数据库，引擎，类型，创建时间等信息

- **COLUMNS**

  提供了表中的信息，详细记录了每个表中的所有列以及列信息

- **STATISTICS**

  记录了表的索引信息



## 3. 常用函数

### 3.1 字符串函数

- `concat(S1, S2, ...Sn)`将S1...Sn的字符串进行拼接

  ```mysql
  mysql> select concat("hello", "\tworld", "\tMysql");
  +---------------------------------------+
  | concat("hello", "\tworld", "\tMysql") |
  +---------------------------------------+
  | hello	world	Mysql                     |
  +---------------------------------------+
  1 row in set (0.00 sec)
  ```

- `INSERT(str, x, y, instr)`将str中从第x个字符开始一直到第y个字符替换为instr

  ```mysql
  mysql> select insert("hello Mysql", 7, 5, 'Python');
  +---------------------------------------+
  | insert("hello Mysql", 7, 5, 'Python') |
  +---------------------------------------+
  | hello Python                          |
  +---------------------------------------+
  1 row in set (0.00 sec)
  ```

- `LOWER(str)`字符串转小写

- `UPPER(str)`转大写

- `LEFT(str, x)`返回字符串最左边的x个字符

  ```mysql
  mysql> select left("hello mysql", 5);
  +------------------------+
  | left("hello mysql", 5) |
  +------------------------+
  | hello                  |
  +------------------------+
  1 row in set (0.00 sec)
  ```

- `RIGHT(str, x)`返回字符串最右边的x个字符

- `LPAD(str,n pad)`使用字符串pad从最左边开始填充str,直到str长度n时结束

  ```mysql
  mysql> select lpad("mysql",9,"hello");
  +-------------------------+
  | lpad("mysql",9,"hello") |
  +-------------------------+
  | hellmysql               |
  +-------------------------+
  1 row in set (0.00 sec)
  ```

- `RPAD(str, n, pad)`使用字符串pad从最右边开始填充str,直到str长度n时结束

- `LTRIM(str)`删除左边的空格

- `RTRIM(str)`删除右边的空格

- `REPEAT(str,x)`返回str重复x的次数的结果

  ```mysql
  mysql> select REPEAT("aa",2);
  +----------------+
  | REPEAT("aa",2) |
  +----------------+
  | aaaa           |
  +----------------+
  1 row in set (0.00 sec)
  ```

- `STRCMP(s1,s2)`比较字符串s1,s2

  如果s1比s2大返回1，否则-1，相等返回0。比较ascii码大小。可以比较数字。无法比较中文

- `TRIM(str)`去掉开头和结尾的空格

- `SUBSTRING(str,x,y)`返回字符串从x开始y个字符的结果

### 3.2 数值函数

主要用于数值的运算操作

- `ABS(x)`返回x的绝对值

- `CEIL(x)`返回大于x的最小整数数值,向上取整

- `FLOOR(x)`返回小于x的最大整数数值,向下取整

- `MOD(x,y)`返回x/y的模

- `RNAD()`返回一个0～1的随机数

- `ROUND(x,y)`返回参数x的四舍五入的有y位小数的值

  ```mysql
  mysql> select round(1.345,2);
  +----------------+
  | round(1.345,2) |
  +----------------+
  |           1.35 |
  +----------------+
  1 row in set (0.00 sec)
  ```

- `TRUNCATE(x,y)`返回数字x截断为y位小数的结果

  ```mysql
  mysql> select truncate(1.345,2);
  +-------------------+
  | truncate(1.345,2) |
  +-------------------+
  |              1.34 |
  +-------------------+
  1 row in set (0.00 sec)
  ```

### 3.3 日期和时间函数

- `CURDATE()`返回当前日期

  ```mysql
  mysql> select CURDATE();
  +------------+
  | CURDATE()  |
  +------------+
  | 2019-08-28 |
  +------------+
  1 row in set (0.00 sec)
  ```

- `CURTIME()`返回当前时间

  ```mysql
  mysql> select CURTIME();
  +-----------+
  | CURTIME() |
  +-----------+
  | 12:53:19  |
  +-----------+
  1 row in set (0.00 sec)
  ```

- `NOW()`返回当前时间和日期

- `UNIX_TIMESTAMP(date)`返回日期date的unix时间戳

  ```mysql
  mysql> select UNIX_TIMESTAMP(now());
  +-----------------------+
  | UNIX_TIMESTAMP(now()) |
  +-----------------------+
  |            1566996885 |
  +-----------------------+
  1 row in set (0.00 sec)
  ```

- `FROM_UNIXTIME(timestamp)`返回unix时间戳的日期,时间戳转日期

  ```mysql
  mysql> select FROM_UNIXTIME(1566996885);
  +---------------------------+
  | FROM_UNIXTIME(1566996885) |
  +---------------------------+
  | 2019-08-28 12:54:45       |
  +---------------------------+
  1 row in set (0.00 sec)
  ```

- `WEEK(date)`返回日期date为一年中的第几周

- `YEAR(date)`返回日期中的年份

- `HOUR(time)`返回日期的小时

- `MINUTE(time)`返回分钟

- `MONTHNAME(date)`返回月份名

- `DATE_FORMAT(date,fmt)`格式化日期

- `DATE_ADD(date,INTERVAL expr type)`返回一个日期或时间值加上一个世界间隔的时间值

  返回当前时间，和距离当前时间31天后的，以及1年2月后的日期

  ```mysql
  mysql> select now() current, date_add(now(), interval 31 day) after41days, date_add(now(),interval '1_2' year_month) after_oneyear_twomonth;
  +---------------------+---------------------+------------------------+
  | current             | after41days         | after_oneyear_twomonth |
  +---------------------+---------------------+------------------------+
  | 2019-08-28 13:38:01 | 2019-09-28 13:38:01 | 2020-10-28 13:38:01    |
  +---------------------+---------------------+------------------------+
  1 row in set (0.00 sec)
  ```

- `DATEDIFF(expr,expr2)`计算两个日期的差的天数

| %a   | 缩写星期名                                     |
| ---- | ---------------------------------------------- |
| %b   | 缩写月名                                       |
| %c   | 月，数值                                       |
| %D   | 带有英文前缀的月中的天                         |
| %d   | 月的天，数值(00-31)                            |
| %e   | 月的天，数值(0-31)                             |
| %f   | 微秒                                           |
| %H   | 小时 (00-23)                                   |
| %h   | 小时 (01-12)                                   |
| %I   | 小时 (01-12)                                   |
| %i   | 分钟，数值(00-59)                              |
| %j   | 年的天 (001-366)                               |
| %k   | 小时 (0-23)                                    |
| %l   | 小时 (1-12)                                    |
| %M   | 月名                                           |
| %m   | 月，数值(00-12)                                |
| %p   | AM 或 PM                                       |
| %r   | 时间，12-小时（hh:mm:ss AM 或 PM）             |
| %S   | 秒(00-59)                                      |
| %s   | 秒(00-59)                                      |
| %T   | 时间, 24-小时 (hh:mm:ss)                       |
| %U   | 周 (00-53) 星期日是一周的第一天                |
| %u   | 周 (00-53) 星期一是一周的第一天                |
| %V   | 周 (01-53) 星期日是一周的第一天，与 %X 使用    |
| %v   | 周 (01-53) 星期一是一周的第一天，与 %x 使用    |
| %W   | 星期名                                         |
| %w   | 周的天 （0=星期日, 6=星期六）                  |
| %X   | 年，其中的星期日是周的第一天，4 位，与 %V 使用 |
| %x   | 年，其中的星期一是周的第一天，4 位，与 %v 使用 |
| %Y   | 年，4 位                                       |
| %y   | 年，2 位                                       |

### 3.4 流程函数

- `IF(value, t, f)`如果value为真则返回t，否则返回f

  ```mysql
  mysql> select if(1>2,'t','f');
  +-----------------+
  | if(1>2,'t','f') |
  +-----------------+
  | f               |
  +-----------------+
  1 row in set (0.00 sec)
  ```

- `IFNULL(value1,value2)`如果value1不为空返回value1，否则返回value2

- `CASE WHEN [value1] THEN[result]...ELSE [default]END`如果value1为真返回result,否则返回default

  ```mysql
  mysql> select case when 1>2 then 't' else 'f' end;
  +-------------------------------------+
  | case when 1>2 then 't' else 'f' end |
  +-------------------------------------+
  | f                                   |
  +-------------------------------------+
  1 row in set (0.01 sec)
  ```

- `CASE [expr] WHEN [value1] THEN [result]...ELSE [deafult] END`如果expr等于value1返回result,否则返回default，类似Java中的case

### 3.5 其他常用函数

- **DATABASE()**返回当前数据库名
- **VERSION()**返回当前数据库版本
- **USER()**返回当前登陆用户名
- **INET_ATON(IP)**返回IP用数字表示
- **INET_NTOA(num)**返回数字代表的ip
- **PASSWORD(str)**返回字符串str的加密版本
- **MD5()**返回字符串的md5值



##  3. 存储引擎

![image-20190829142045705](https://ly-markdown.oss-cn-shenzhen.aliyuncs.com/image-20190829142045705.png)

### 3.1 MyISAM

​	缺点: 不支持事务，外键，不适合修改

​	优点: 查询效率快

​	损坏后可以通过check table 检查myisam表的健康性

#### 3.1.1 存储格式

- 静态表(固定长度)

  每个记录的都是固定长度的，会自动删除字符串后面的空格

- 动态表

  包含了变长字段，每条数据长度不一样，更具实际长度进行保存。如果频繁进行删改操作回产生大量的碎片，

  需要定期执行`OPTIMIZE TABLE`或者`myisamchk -r`用于改善性能

- 压缩表

  用`myisampack tablename`工具创建，磁盘占用小，因为每个记录都是单独压缩的

  ```mysql
  # 测试压缩表的效果
  create database myisamdb;
  use myisamdb;
  create table myisam
  (id int unsigned auto_increment primary key,
  name varchar(20) not null )engine=myisam charset=utf8;
  # 插入数据
  insert into myisam(name) values('联想tinkpad'),('Dell外星人'),('雷神玄武'),('mac pro');
  # 反复执行下面语句，让数据量迅速膨胀
  insert into myisam select null,name from myisam;
  ```

  ```shell
  # 查看压缩前大小
  root@mysql:/var/lib/mysql/test# ls -lh myisam*
  -rw-rw---- 1 mysql mysql 8.4K Aug 29 07:15 myisam.frm
  -rw-rw---- 1 mysql mysql  12M Aug 29 07:15 myisam.MYD
  -rw-rw---- 1 mysql mysql 5.2M Aug 29 07:15 myisam.MYI
  # 执行压缩语句
  root@mysql:/var/lib/mysql/test# myisampack  myisam
  Compressing myisam.MYD: (524288 records)
  - Calculating statistics
  - Compressing file
  60.38%     
  Remember to run myisamchk -rq on compressed tables
  # 查看压缩后的大小
  root@mysql:/var/lib/mysql/test# ls -lha myisam.*
  -rw-r----- 1 mysql mysql 4.0M Aug 29 07:17 myisam.MYD
  -rw-r----- 1 mysql mysql 1.0K Aug 29 07:22 myisam.MYI
  -rw-r----- 1 mysql mysql 8.4K Aug 29 07:17 myisam.frm
  # 由于压缩后，表的空间会发生变化，会导致索引无法找到数据，所以需要恢复索引
  root@mysql:/var/lib/mysql/test# myisamchk -rq myisam
  - check record delete-chain
  - recovering (with sort) MyISAM-table 'myisam'
  Data records: 524288
  - Fixing index 1
  # 复原后的大小
  root@mysql:/var/lib/mysql/test# ls -lh myisam.*
  -rw-r----- 1 mysql mysql 4.0M Aug 29 07:17 myisam.MYD
  -rw-r----- 1 mysql mysql 5.1M Aug 29 07:31 myisam.MYI
  -rw-r----- 1 mysql mysql 8.4K Aug 29 07:17 myisam.frm
  ```

### 3.2 InnoDB

​	优点: 支持事务，外键

#### 3.2.1 存储方式

- 共享表空间存储，将表的结构保存在`.frm`文件中,数据和索引存在**innodb_data_home_dir**和**innodb_data_file_path**定义的表空间中，可以是多文件

- 使用多表空间进行存储

  表的结构依然保存在`.frm`文件中，但是每个表的数据和索引单独保存在`.ibd`中。如果是分区表，则每个分区对应单独的`.ibd`文件，文件名为"表名+分区名"，可以在创建分区时，指的每个分区的文件路径，以保证将表的IO均匀的分布在多个磁盘中

### 3.3 MEMORY

​	基于内存的引擎，将数据保存至内存中以提高访问效率，默认为HASH索引，但是服务器一旦关闭。则将丢失数据。

## 4. 数据类型

### 4.1 char 和 varchar

![image-20190829160514772](https://ly-markdown.oss-cn-shenzhen.aliyuncs.com/image-20190829160514772.png)

- MyISAM

  固定长度

- MEMORY

  固定长度，无论是设置的char或者varchar，一律作为固定长度处理

- InnoDB

  varchar类型，因为内部并没有区分固定和可变长度

### 4.2 TEXT与BLOB

​	使用这两个数据类型时，如果遇到删除操作，会在数据表中留下很大的“**空洞**”。以后往空洞插入数据在性能上会有一定的影响。建议定义使用`OPTIMIZE TABLE`功能进行碎片管理。

​	如果在删除后没有及时的对碎片空间进行处理，会导致碎片空间一直占有磁盘，即使在数据库中删除了对应的数据，在磁盘上对应的文件大小并不会立即改变。

如果使用这两个字段进行存储并需要进行索引时，建议使用合成索引。

合成索引: 将原来的数据进行提取散列值进行存储，需要查询也通过散列值查询，以便提高效率

- **TEXT**

  只能保存字符数据

- **BLOB**

  可以保存二进制数据

### 4.3 浮点数与定点数

- **Float**

  超过精度会进行数据截断，四舍五入的方式, 存在精度损失问题

- **Decima**

  超过精度直接截断，有较高的精度

## 5. 字符集

### 5.1 查看mysql支持的所有字符集

`show character set`

### 5.2 设置字符集

- 设置默认字符集

  在`my.cnf`中设置

  ```shell
  [mysqld]
  character-set-server=utf8
  # 设置默认连接的字符集为utf8
  default-character-set=utf8
  ```

- 启动时指定

  ```mysql
  mysqld --character-set-server=utf8
  ```

- 编译时指定

  ```shell
  cmake . -DDEFAULT_CHARSET=utf8
  ```

### 5.3 查看默认字符集与校验规则

```mysql
# 字符集
show variables like 'character_set_database';
# 字符集的校验规则
show variables like 'collation_database';
```

### 5.3 修改字符集

1. 到出表结构

   ```shell
    mysqldump -uroot -p -d [databasename]> createtab.sql
    # -d 表示只导出结构，不导出数据
   ```

2. 手动修改createtab.sql中定义表结构的字符集

3. 导出数据

   ```shell
   mysqldump -uroot -p --quick --no-create-info --extended-insert [databasename]>data.sql
   ```

   - **Quick**

     用于体量较大的表

   - **Extended-insert**

     导出后的数据可以快速加载插入

   - **no-create-info**

     不导出数据对应表的结构

   - **default-character-set**

     设置默认的字符集，避免导出时候乱码

4. 将data.sql里的`set names latin1`修改为`set names utf8`

5. 使用新得字符集创建新的数据库

6. 创建表

   ```mysql
   mysql -uroot -p [databasename]< createtab.sql
   ```

7. 导入数据

   ```shell
   mysql -uroot -p [databasename]<data.sql
   ```

## 6. 索引

### 6.1 索引的使用

​	InnoDB限制的前缀索引不超过767个字节

​	MyISAM限制前缀索引不超过1000字节

- 创建表时指定

  ```mysql
  # 创建一个索引名为name，索引对应的字段为name，并取前十个字符作为前缀索引
  create table index_test(
  name varchar(20),
  unique name (name(10))
  );
  ```

- 为创建好的表添加索引

  ```mysql
  create unique index indexName on tablename(fieldName(length));
  ALTER table tablename ADD UNIQUE indexName (fieldName(length));
  ```

- 查看表的索引信息

  ```mysql
  SHOW INDEX FROM table_name; \G
  ```

- 删除索引

  ```mysql
  drop index index_test on name;
  ```

### 6.2 索引设计原则

- 尽量选择搜索列
- 尽量设置唯一索引
- 短索引(前缀索引)
- 利用最左索引，即创建多列索引时需要最好使用靠左边的列进行匹配，也称为最左索引
- 适度的设置索引，不要过多创建，创建大量的索引会占用大量的额外空间，降低了写操作
- InnoDB引擎表中尽量自己设置主键，因为如果没有设置主键，但是存在唯一索引，则在存储中会按照唯一索引进行排序保存，否则会自动生成一个内部列，按照这个列的顺序进行存储。

### 6.3 BTREE索引与HASH索引

#### 6.3.1 HASH索引

HASH索引注意场景

- 只能使用**=/<=>**两个操作符
- 优化器不能使用HASH索引加速`ORDER BY`操作
- 不能确定两个值之间有多少行，如果将一个**MyISAM**表改为**HASH**索引的**MEMORY**表，会影响一些查询效率
- 只能使用整个关键词搜索一行

#### 6.3.2 BTREE索引

支持的查询

- `>/<`
- `>=/<=`
- `between x and y`
- `!=/<>`
- `like `不以通配符开始

## 7. 视图

视图是一个虚拟存在的表，视图中的数据是在使用视图时动态生成的

​	优点

- **简单**，用户无需关系表后对应的结构，因为视图就是经过筛选后的
- **安全**, 使用视图的用户只能允许被访问允许查询的结果集，单纯的使用表的权限，不能限制到某一行，但是视图可以
- **数据独立**，一旦视图结构确定，可以屏蔽表结构变化对用户的影响，原表增加列对视图没有影响，修改删除，只需要修改视图即可

### 7.1 创建视图

```mysql
create view viewname as select * from tablename;
```

### 7.2 删除视图

```mysql
drop view viewname;
```

## 8. 事务控制和锁定语句

### 8.1 LOCK TABLE 和 UNLOCK TABLE

Lock table 用于锁定当前表

Unlock table 用于释放当前表

### 8.2 事务控制

事务通过隔离级别进行事务的管理

## 9. SQL优化

### 9.1 查看SQL语句执行频率

```mysql
show [global|session] status like 'Com_%';
```

查询mysql启动至今(**global**)或者当前连接(**session**)的sql语句统计结果,默认查询当前连接

- `Com_select`执行select操作次数
- `com_insert`执行insert操作次数
- `com_update`执行update操作次数
- `com_delete`执行delete操作次数
- `Innodb_rows_read`select查询返回的行数
- `Innodb_rows_inserted`执行insert操作插入的行数
- `Innodb_rows_updated`执行update操作更新行数
- `Innodb_rows_deleted`执行delete操作删除的行数

- `Connections`试图连接Mysql服务器的次数
- `Uptime`服务器工作时间
- `Slow_queries`慢查询次数

### 9.2 定位执行效率较低的SQL语句

- 通过慢查询日志定位，通过`--log-slow-queries[=file_name]`选项启动可以mysqld会写一个包含所有执行时间超过`long_query_time`秒的SQL语句的日志文件
- `show processlist`查看当前Mysql在执行的线程，包括线程状态，是否锁表等

### 9.3 通过EXPLAIN分析低效SQL的执行计划

```mysql
explain [sql]
```

- `select_type`select的类型，常见`SIMPLE`(简单表, 即不存在子查询和管理查询), `PRIMARY(主查询，即外层查询)`, `union(union中第二个或者后面的查询语句)`, `subquery(子查询中的第一个select)`

- `table`:输出结果集的表

- `type`: 表示Mysql在表中找到所需行的方式，或者访问类型

  从左到右，性能由差到好

  ```mysq
  all -> index -> range -> ref -> eq_ref -> const/system -> null
  ```

  - **all**

    全表扫描

  - **index**

    索引全扫描

  - **Range**

    索引范围扫描

  - **ref**

    使用唯一索引扫描或唯一索引前缀扫描，匹配单值

  - **eq_ref**

    多表连接中使用`primary key`或者`unique index`作为条件

  - **const**/**system**

    单表中最多只有一个匹配行，查询非常快

  - **NULL**

    无需访问表或者索引，直接能得到结果

- **possible_keys**: 查询时可能用到的索引

- **key**: 实际使用的索引

- **key_len**: 使用到索引字段的长度

- **rows**: 扫描行的数量

- **Extra**: 执行情况的说明和描述，包含不适合在其他列中显示但对执行计划很重要的额外信息

### 9.4 通过show profile分析SQL

通过开启`profiling`可以查看SQL执行的过程

```mysql
# 查看是否开启 1开启 0关闭
select @@profiling;
# 开启
set profiling=1;
# 执行sql语句
select count(*) from test;
# 查看sql语句的查询id
show profiles;
# 找到对应sql语句的id,并查询sql详细数据,100是我查出的sql执行id
show profile for query 100;
# 将结果进行排序整理
select state,sum(duration) as total_r, 
		round(
			100 * sum(duration) / 
				(select sum(duration) 
				from `information_schema`.profiling
				where query_id = @query_id
				),2
		) as pct_r,
		count(*) as calls,
		sum(duration) / count(*) as "r/call"
	from `information_schema`.profiling
	where query_id = @query_id
	group by state
	order by total_r desc;

#查看cpu耗时
show profile cpu for query 100;
```

### 9.5 SQL提示

通过在sql语句中插入人为的提示已到达优化的目的

```mysql
# 强制mysql生成一个临时结果集，生成临时结果集后，所有表上的锁定都会被释放
select SQL_BUFFER_RESULT * from ....
```

#### 9.5.1 use index

在查询语句中表名的后面添加`use index` 来提供希望Mysql去参考的索引列表，就可以让MySQL不再考虑其他可用的索引

```mysql
select * from test use index (indexnmae);
```

#### 9.5.2 ignore index

忽略指定索引

```mysql
select * from test ignore index (indexnmae);
```

#### 9.5.3 force index

强制使用特定索引

```mysql
select * from test force index(indexnmae);
```

## 10. SQL优化

### 10.1 随机获取某一行

```mysql
select * from test order by rand();
```

### 10.2 **WITH ROLLUP**

能够更加详细的进行分组，和**ORDER BY**互斥，不能同时使用，同时**LIMIT**必须在**ROLLUP**后面 

### 10.3 bit_or

与运算，将数字转换为二进制如果都为1则是1

## 11. 优化数据库对象

### 11.1 优化表的数据类型

```mysql
# 分析表的数据类型，是否需要进行优化
select * from table_name PROCEDURE ANALYSE();
# 不要为那些包含的值多于16个或者256个字节的enum类型提出建议
select * from table_name PROCEDURE ANALYSE(16,256);
```

通过改函数进行分析表会展示改表中每个字段**的最大长度和最小长度**，**以及平均长度**，还有修改建议，将该字段改为什么类型的建议

### 11.2 拆分表提高表的访问效率

即分表，将数据表进行拆分

- **垂直拆分**

  即把主键和一些列放到一个表，然后把主键和另外的列放入另一个表

- **水平拆分**

  根据一列或多列数据的值把数据放到两个独立的表中

**使用情况**：

- 表很大，分割后可以降低在查询时需要读的数据和索引的页数，同时降低了索引的层数，提高查询速度
- 表中s护具本就具有独立性，如: 表中分别记录各个地区的数据或不同时期的数据，特别是有些数据常用有些不常用
- 需要把数据存放到多个介质上。

### 11.3 逆规范化

因为规范化越高，那么产生的关系就越多，关系过多的直接影响查询速度，因为涉及的表操作也越多。但是无论怎么违法规范都要保证在数据完整性的条件下进行

- **增加冗余列**

  在多个表中具有相同的列，它常用来在查询时避免查询操作

- **增加派生列**

  增加的列来自其他表中的数据，由其他表中的数据经过计算得出，减少连接操作和集函数

- **重新组表**

  如果许多用户需要查看两个表连接出来的结果数据，则把这两个表重新组成一个表来减少连接而提高性能

- **分割表**

  分表，增加查询效率

保证数据完成性常用的方法是，**批处理维护**，**应用逻辑和触发器**

- **批处理维护**

  指对复制列或派生列的修改积累一定时间后，通过一批处理作业或存储过程对复制或派生列进行修改，仅限在对实时性要求不高的场景

- **应用逻辑**

  在同一事物中索引涉及表进行增删改操作，都要做逻辑处理，这样风险较大，并且不易维护

- **触发器**

  对数据有任何修改立即触发对复制列或派生列的修改

### 11.4 使用中间表提高统计查询速度

当单表数据量较大，有需要对部分数据进行统计时，采用中间表可以减少时间，同时可以避免查询操作对原表有影响。同时中间表可以自由添加索引或者字段进行查询以提高效率。

## 12. 锁

- 表级锁

  **开销小，加锁快，不会出现死锁，粒度大，冲突概率大，并发低**

- 行级锁

  **开销大，加锁慢，会出现死锁，粒度小，冲突概率低，并发高**

### 12.1  MyISAM

#### 12.1.1 查询表级锁的争用情况

```mysql
show status like 'table%';
```

如果`Table_locks_waited`的值较高，则说明存在着严重的表级锁争用情况

#### 12.1.2 mysql表级锁的锁模式

- 共享锁

  可以共享读，但不允许写

- 表独占写锁

  一个线程在写的时候不允许其他线程读或者写

#### 12.1.3 如何加锁

**MyISAM**在执行查询语句前，会自动给涉及的所有表加读锁，在执行更新操作前会自动对涉及的表进行加锁

**显式加锁**

```mysql
Lock tables tablename1 read local, tablename2 read local;
select * from tablename1;
select * from tablename2;
unlock tables;
```

**特别注意**:

执行`lock tables`后，只能访问加锁的表，不能访问未加锁的表，同时，如果加的是读锁那么只能读，不能更新。

并且在一个sql语句中表出现几次就要加锁几次，使用别名也会出现错误，在加锁时必须设置对应的别名

### 12.2 InnoDB

InnoDB通过事物已经事物的隔离性来应对锁的问题, InnoDB是行锁是基于索引实现的，所以不通过索引访问数据，InnoDB会对所有数据进行加锁

#### 12.2.1 查看InnoDB行锁争用情况

```mysql
show status like 'innodb_row_lock%';
```

如果争夺较高则**InnoDB_row_lock_waits**和**InnoDB_row_lock_time_avg**的值较高，可以通过查询`information_schema`数据库中相关表来查看锁情况，或者通过设置**InnoBD Monitors**来进一步观察发生冲突的表，数据行等

- 通过`information_schema`查看

  ```mysql
  select * from innodb_locks;
  ```

- 设置`InnoDB Monitors`观察锁冲突

  ```mysql
  # 创建监听器
  create table innodb_monitor(a INT) engine=innodb;
  # 查看具体情况
show engine innodb status;
  # 停止
  drop table innodb_monitor;
  ```
  

#### 12.2.2 什么时候使用表锁

- 事务需要**更新大部分或全部数据**，表有比较大，如果默认是行锁那么事务的效率将执行底下，而且会造成其他事务等待时间过长以及锁冲突
- **涉及多个表，**比较复杂很可能引起死锁，造成大量事务回滚，使用表锁可以减少事务回滚所导致的开销

**注意事项**：

1. 使用`LOCK TABLES`虽然可以给InnoDB加表级锁，但必须说明的是，表锁不是由InnoDB存储引擎管理的，而是由MysqlServer负责，**仅当**`autocommit=0,innodb_table_locks=1`时InnoDB层才能知道MySQL加的表锁，MysqlServer也才能感知InnoDB加的行锁，这种情况InnoDB才能自动识别涉及表级锁的死锁，否则InnoDB无法自动检测并处理这种死锁
2. 使用`LOCK TABLES`对InnoDB进行加锁时要将设置`autocommit=0`否则Mysql不会给表加锁，事务结束前，不要用`UNLOCK TABLES`释放表锁，因为`UNLOCK TABLES`会隐性的提交事务，`commit`或`rollback`并不会释放`lock tables`的表锁

###  12.3 死锁

MyISAM中查询总是一次获取所有所需的表锁，要么全部满足，要么全部等待，因此很难出现死锁的情况。但在InnoDB中，锁时逐步获得的，因此决定了存在发生死锁的情况

#### 12.3.1 避免死锁的方案

1. 如果多个程序并发存取多个表，应尽量以相同的顺序来访问表，这样可以大大降低产生死锁的机会
2. 合理事务设计

## 13 MySQLServer优化

### 13.1 内存优化原则

- 尽可能将多的内存分配给MySQL做缓存，但要给操作系统和其他程序预留足够的内存
- 如果有MyISAM表，就要预留更多的内存给操作系统做IO缓存

### 13.2 MyISAM内存优化

1. `key_buffer_size`设置

   改设置决定了MyISAM索引块缓存区的大小，直接影响了MyISAM表的存储效率

2. 使用多个索引缓存

   MySQL通过session共享的key buffer提高了MyISAM索引存取的性能，但并不能消除seesion间对key buffer的竞争。如果某个session对某个很大的索引进行扫描，就可能将其他的索引数据块挤出索引缓存区。解决方案是不同的表索引缓存到不同的key buffer中

   ```mysql
   # 创建新的key buffer. hot_cache是新建索引缓存的名称，global代表对所有连接生效
   set global hot_cache.key_buffer_size=128*1024;
   # 删除
   set global hot_cache.key_buffer_size=0;
   ```

3. 调整"**中点插入策略**"

   默认情况，MySQL是使用LRU(最近最少未使用淘汰算法)，但在某种情况会不太合适，会导致热数据被淘汰。

   **解决方案**:

     将LRU链表分为`hot`子表和`warm`子表，当一个索引块读入内存时，先被放到链表的中点，即`warm`子表的尾部，当命中一定次数后，该索引块会被移入到`hot`子表尾部，此后该数据块在`hot`子表流转，当其达到`hot`子表头部并超过一定时间后，将由`hot`头部转移到`warm`子表的头部，当需要淘汰索引块时，缓存管理程序会选择优先淘汰`warm`表头部的内存块。

   **实际操作**：

   ```mysql
   # 使用30%作为最热索引块
   set global key_cache_division_limit = 70;
   set global hot_cache.key_cache_divison_limit = 70;
   ```

4. 调整`read_buffer_size`和`read_rmd_buffer_size`

   如果需要经常顺序扫描`MyISAM`表，可以通过增大`read_buffer_size`的值改善性能，注意`read_buffer_size`时session独占的，所以默认值太大会造成内存浪费

### 13.3 InnoDB内存优化

1. InnoDB缓存

   InnoDB使用的LRU算法与MyISAM的“中点插入策略”很类似，将LRU分为`young sublist`和`old sublist`,当放入新的缓存块时，缓存块会插入LRU list的中点，即`old sublist`的头部，经过`innodb_old_blocks_time`参数时间，改数据块会从`old sublist`转移到`young sublist`的头部，也就是`LRU list`的头部，随着时间的推移，`young sublist` 和`old sublist`中较少访问的缓存块从各自链表的头部移动到尾部，当需要淘汰缓存块时，优先从链表尾部进行淘汰。

2. Innodb_buffer_pool_size

   该参数决定了InnoDB存储引擎表数据和索引数据的最大缓存区大小，Innodb buffer pool 同时为数据块和索引提供数据缓存，innodb_buffer_pool_size的值越大，缓存命中率越高。

3. 调整`old sublist`大小

   `old sublist`的大小由系统参数`innodb_old_blocks_pct`决定，取值范围是5～95，默认为37

   ```mysql
   # 查看old sublist的大小
   show global variables like '%innodb_old_blocks_pct%';
   ```

   如果`young/s`值很低，需要适当增大`innodb_old_blocks_pct`的值或减少`innodb_old_blocks_times`的值

4. Innodb_old_blocks_times

   此参数决定了缓存数据块由`old sublist`转移到`young sublist`的快慢,

   如果进行表扫描时`non-youngs/s`很低，`young/s`很高，就应该考虑将该参数调大

5. 调整缓存池数量，减少内部对缓存池数据结构的争用

   不同线程对InnoDB缓存池的访问在某些阶段是互斥的，这种内部竞争也会产生性能问题，尤其在高并发和buffer pool较大的情况。如果缓存池较大需要适当增加`innodb_buffer_pool_instances`配置参数，可以降低并发导致的内部冲突，改善性能.InnoDB会将参数`innodb_buffer_pool_size`指定大小的缓存平分为`innodb_buffer_pool_instances`个`buffer pool`

6. 控制innodb buffer刷新，延长数据缓存时间，减缓磁盘I/O

   当InnoDB找不到干净可用缓存页或检查点被触发时，InnoDB**后台线程就会开始把脏的缓存页**回写到磁盘文件，这个过程叫**缓存刷新**

   控制缓存刷新的快慢主要取决于两个参数

   - **innodb_max_dirty_pages_pct**

     它控制缓存池中脏页的最大比例，默认是75%，如果脏页的数量达到或超过该值就会进行缓存刷新

   - **Innodb_io_capacity**

     代表磁盘每秒可完成I/O的次数，默认值为200

### 14 InnoDB log机制及优化

 		InnoDB通过`redo log`机制来保证事务更新的一致性和持久性。

### 14.1 InnoDB重做日志

​		当更新数据时，InnoDB内部的操作流程大致是:

		1. 将数据读入InnoDB buffer pool 并对相关记录加独占锁
  		2. 将UNDO信息写入undo表空间的回滚段中
  		3. 更改缓存页的数据，并更新记录写入redo buffer中
  		4. 提交时，根据innodb_flush_log_at_trx_commit的设置，用不同的方式将redo buffer中的更新记录刷新到InnoDB redo log file中，然后释放独占锁
  		5. 最后, 后台IO线程根据需要择机将缓存中更新过的数据刷新到磁盘文件中

### 14.2 innodb_flush_log_at_trx_commit

 		该参数可以控制将`redo buffer`中的更新记录写入到日志文件以及将日志文件数据刷新到磁盘的操作时间。通过调整这个参数，可以在性能和数据安全之间做取舍

- 如果参数为0，事务提交时，InnoDB不会立即出发将缓存日志写入到磁盘文件的操作，而是每秒触发一次缓存日志回写磁盘操作，并调用操作系统fsync刷新IO缓存
- 如果参数为1，每个事务提交时，InnoDB立即将缓存中的redo日志回写到日志文件，并调用操作系统fsync刷新IO缓存
- 如果参数为2，每个事务提交时，InnoDB立即将缓存中的redo日志回写到日志文件，但并不马上调用fsync来刷新IO缓存，而是每秒只做一次磁盘IO缓存刷新操作

 参数的默认值时0，保证了数据的安全性，但是损失了性能

### 14.3 设置log file size 控制检查点

​		当日志文件写满后会自动切换到另一个日志文件，但切换时会触发数据库检查点，导致InnoDB缓存脏页的小批量刷新，会明显降低InnoDB性能。

​		通过计算每小时产生的日志量估算合适的innodb_log_file_size值

### 14.4 innodb_log_buffer_size

   	innodb_log_buffer_size 决定InnoDB重做日志缓存池的大小，默认为8mb,如果存在可能产生大量更新记录的事物，可以增加innodb_log_buffer_size的大小

### 14.5 调整MySQL并发相关参数

主要参数包括，max_connections, back_log, thread_cache_size, table_open_

#### 14.5.1 max_connections 提高并发连接

 默认值为151，如果状态变量`connection_errors_max_connections`不为零，并且一直在增长，就说嘛不断有连接请求因数据库连接以达到最大允许值而失败。

#### 14.5.2 back_log

 控制MySQL监听TCP端口时设置的积压请求栈大小，5.6.6以前默认时50，以后时50+(max_connections/5)但最大不能超过900。如果需要数据库在较短时间内处理大量连接请求，可以适当考虑增加back_log的值

#### 14.5.3 table_open_cache

每个SQL执行线程至少都要打开一个缓存表，参数table_open_cache控制所有SQL执行线程可打开表缓存的数量，这个参数的值应根据最大连接数max_connection以及每个连接执行管理查询中所涉及的表最大个数(N)

```
max_connections * N
```

如果opened_tables的值较大，就说名table_open_cache设置的太小，应该适当增大。如果过大，需要评估`open-files-limit`的设置是否够用。

#### 14.5.4 调整thread_cache_size

​	为了增加连接数据库的速度，MySQL会缓存一定数量的客户端服务线程以备重用，通过参数thread_cache_size可控制MySQL缓存客户服务线程的数量

#### 14.5.5 innodb_lock_wait_timeout

​	参数innodb_lock_wait_timeout 可以控制innodb事物等待行锁的时间，默认值为50ms,

可以更具需求动态设置。如果需要快速反馈的，可以将行锁等待时间间隔调小，避免事物长时间挂起，如果是后台的批处理操作，可以将行锁等待超时时间调大，避免大事务发生回滚。