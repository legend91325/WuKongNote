#  Linux系统下授权MySQL账户访问指定数据库

标签（空格分隔）： linux mysql database
---
##需求：
1、在MySQL中创建数据库mydata
2、新建MySQL账户admin密码123456
3、赋予账户admin对数据库mydata具有完全操作权限
操作如下：
1. 登录MySQL数据库
     ` mysq  -uroot  -p `   #在终端命令行输入
      `123456`       #输入密码
2. show databases;     #显示数据库列表
3. create database mydata;     #建立数据库mydata
4. insert into mysql.user(Host,User,Password) values('localhost','admin',password('123456'));    #新建账户admin，密码123456
5. flush privileges;    #刷新系统授权表
6. grant all on mydata.* to 'admin'@'%' identified by '123456' with grant option;    #允许账户admin从任何主机连接到数据库mydata
至此，账号admin对数据库mydata具有完全管理权限。
扩展：
     ` grant all on mydata.* to 'admin'@'192.168.1.1' identified by '123456' with grant option;`
账户admin只能从192.168.1.1连接到数据库mydata 
      `grant all on mydata.* to 'admin'@'localhost' identified by '123456' with grant option;` 账户admin只能从本机连接到数据库mydata
      `update mysql.user set password=password(1234) where User="admin" and Host="localhost";`   修改账号admin密码为1234
     `revoke all on mydata.* from 'admin'@'%';`
禁止用户admin从任何主机访问数据库mydata 
    `revoke all on mydata.* from 'admin'@'192.168.1.1'; `  禁止用户admin从192.168.1.1访问数据库mydata 
   `dalete  from  mysql.user  where  user="admin" and Host="localhost";`    #删除用户admin

####复制数据库
nohup mysqldump dbname1 -h'host1' -uusername -ppassword --opt | mysql dbname2 -h'host2' -uusername -ppassword &