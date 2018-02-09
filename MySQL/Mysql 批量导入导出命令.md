##load data infile

###导入文本文件命令格式：

`load data infile 'filepath' into tablename`。
`LOAD DATA [LOW_PRIORITY | CONCURRENT] [LOCAL] INFILE 'file_name.txt'
 [REPLACE | IGNORE] INTO TABLE tbl_name [FIELDS  [TERMINATED BY 'string'] [[OPTIONALLY] ENCLOSED BY 'char'] [ESCAPED BY 'char' ] ][LINES [STARTING BY 'string'] [TERMINATED BY 'string'] ]
[IGNORE number LINES] [(col_name_or_user_var,...)] [SET col_name = expr,...]]`

###各参数使用：

它通过发送一个LOAD DATA INFILE 命令到服务器来实现。--local 选项使得import 从客户端主机读取数据文件。如果客户端与服务器支持压缩协议，用户可以指定--compress 选项，以在较慢的网络中获得更好的性能。参考管理员手册中的import — 数据导入程序 。

如果用户指定关键词`LOW_PRIORITY`，`LOAD DATA` 语句的执行将会被延迟，直到没有其它的客户端正在读取表。

如果一个GSSYS 表满足同时插入的条件（即该表在中间有空闲块），并且您对这个GSSYS 表指定了`CONCURRENT`，则当`LOAD DATA` 正在执行时，其它线程会从表中重新获取数据。即使没有其它线程在同时使用本表格，使用本选项也会略微影响`LOAD DATA `的性能。

如果指定了`LOCAL` 关键字，它将对连接的客户端做出解释：

- 如果指定了`LOCAL`，客户端主机上的客户端组件读取文件并发送到服务器。可以给出文件的完整路径以确定其精确位置。如果给出的是相对路径，则文件名是相对于客户端组件启动时所在的目录。
- 如果没有指定`LOCAL`，文件是位于服务器的主机上，并且直接被服务器读取。
当从服务器主机定位文件时，服务器使用下列规则：
- 如果给定完整的路径，服务器使用该路径名。
- 如果给定一个或多个前置构件的相对路径，服务器以相对服务器的数据目录搜索文件。
- 如果给定没有前置构件的文件名，服务器从当前数据库的数据库目录搜寻文件。
 注意：
这些规则意味着，一个以`/gsfile.txt` 给出的文件是从服务器的数据目录中读取的，然而，以gsfile.txt 给出的一个文件是从当前数据库的数据目录下读取的。举例来说，下面的`LOAD DATA` 语句从db1 数据库目录下读取文件data.txt，因为db1 是当前数据库，即使语句明确把文件载入到db2数据库中的表里，也会从db1 目录中读取：
`mysql> USE db1;
mysql> LOAD DATA INFILE 'data.txt' INTO TABLE db2.gs_table`
注意：
指定Windows 路径名时，使用的是斜线而不是反斜线。如果要用反斜线，必须双写。
出于安全的原因，当读取位于服务器上的文本文件时，文件必须位于数据库目录下或者可以被所有用户读取。也就是说，当对服务器上的文件执行  LOAD DATA INFILE  时，用户必须获得FILE 权限。


与服务器直接访问文件相比，使用LOCAL 速度稍微慢些，这是因为文件的内容必须通过客户端到服务器的连接传送。另一方面，对于本地文件，不需要获得FILE 权限。

只有服务器和客户端都允许时，LOCAL 才可以工作。例如，如果kernel 启动时，`--local-infile=0`，则LOCAL 不能工作。参考管理员手册中的LOAD DATA LOCAL 的安全问题 。

`REPLACE `和`IGNORE `关键字处理那些与已存在的主键值重复的输入记录。

如果指定了`REPLACE`，输入行将会代替已存在的行（也就是说，主索引值相同的行将作为存在的行）。

如果指定了`IGNORE`，与已存在行主键值重复的输入行将被跳过。如果不指定二者中的任一个，则操作行为将依赖是否指定了LOCAL 关键字。没有指定LOCAL，则如果发现有重复的键值，将产生一个错误，并忽略文本文件的其余部分。如果指定了LOCAL，则缺省的操作行为将与指定了IGNORE 的相同；这是因为，在操作过程中，服务器没有办法终止文件的传送。

如果希望装载操作中忽略外键约束，可以在执行LOAD DATA 之前执行`SET FOREIGN_KEY_CHECKS=0` 语句。

如果用户在一个空的GsSYS 表上使用`LOAD DATA INFILE`，所有非唯一索引会以分批方式被创建（就像REPAIR）。当有许多索引时，这通常可以使`LOAD DATA INFILE` 更快一些。正常情况下非常快，但也有极端的情况，用户可以通过在装载文件之前使用`ALTER TABLE .. DISABLE KEYS` 关闭它们和在装载文件之后使用`ALTER TABLE .. ENABLE KEYS` 重建索引，从而加速索引创建。


##SELECT ... INTO OUTFILE

`SELECT ... INTO OUTFILE`是`LOAD DATA INFILE ` 的反操作。

使用`SELECT ... INTO OUTFILE` 将数据从一个数据库写到一个文件中。使用`LOAD DATA INFILE` 读取文件到数据库中。两个命令的`FIELDS` 和`LINES` 子句的语法是一样的。两个子句都是可选的，但是如果两个同时被指定，FIELDS 子句必须出现在`LINES` 子句之前。

如果用户指定一个`FIELDS` 子句，它的子句 （`TERMINATED BY、[OPTIONALLY] ENCLOSED BY 和ESCAPED BY`) 也是可选的，不过，用户必须至少指定它们中的一个。

如果用户没有指定一个FIELDS 子句，缺省时如同使用下列语句：

`FIELDS TERMINATED BY '\t' ENCLOSED BY '' ESCAPED BY '\\'`

如果用户没有指定一个LINES 子句，缺省时如同使用下列语句：

`LINES TERMINATED BY '\n' STARTING BY ''`

换句话说，当读取输入时，缺省的LOAD DATA INFILE 表现如下：

• 在换行符处寻找行的边界。
• 不遗漏任何行前缀。
• 在制表符处将行分离成字段。
• 不认为字段由任何引号字符封装。
• 将有 “\” 开头的定位符、换行符或`\' 解释为字段值的一个文字字符。

相反的，当写入输出时，缺省值导致`SELECT ... INTO OUTFILE` 表现如下：

• 在字段值间加上制表符。
• 不用任何引号字符封装字段。
• 使用 “\” 转义出现在字段值中的定位符、换行符或`\' 字符实例。
• 在行的结尾处加上换行符。

>注意：为了写FIELDS ESCAPED BY '\\'，用户必须指定两个反斜线，该值会作为一个反斜线被读入。
如果是Windows 系统的文本文件，可能必须使用`LINES TERMINATED BY '\r\n' `来读取文件，这是因为Windows 系统的特点是使用两个字符作为行终止符。在某些程序中，在书写文件时，可能使用\r 作为行终止符，如写字板。读取这类文件时，需要用`LINES TERMINATED BY '\r'`。