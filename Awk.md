### Awk

##### 资料地址：
~~~
Awk Tutorial
https://www.tutorialspoint.com/awk/

AWK 简明教程
https://coolshell.cn/articles/9070.html

IBM--准备好开始使用 GAWK
https://www.ibm.com/developerworks/cn/education/aix/au-gawk/

awk从放弃到入门系列
http://www.zsythink.net/archives/1336
~~~

---

##### 变量参数整理：
~~~变量说明
ARGV 参数变量的数量 包含 awk本身
CONVFMT 浮点类型精度 默认%.6g
OFMT：数值输出格式 默认%.6g
ENVIRON 当前环境变量数组 获取 ENVIRON["变量名"]
FS：字段分隔符 默认空格
OFS：输出字段的分隔符 默认空格
RS：默认的记录的分隔符 默认新行
ORS：默认输出记录的分隔符 默认新行
NF：记录的字段数量
NR：当前执行的记录数量 （理解就是执行到第几行记录）
FNR：与NR相似，指定的是当前文件的记录数量，当awk执行多个文件时会与NR不同概念
RSTART：match函数首次匹配的字符串位置index
RLENGTH：match函数 匹配上的字符串长度
$0：代表整个记录
$n：代表当前记录第n个字段，默认分隔符是FS
~~~