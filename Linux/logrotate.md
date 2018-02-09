# linux下日志管理工具--logrotate

标签（空格分隔）： linux

---

##配置

```linux
/usr/local/nginx/logs/*.log {
    daily   
    dateext 
    rotate 1
    olddir  /usr/local/nginx/other/logs
    missingok  
    notifempty 
    compress 
    sharedscripts 
    postrotate
        /bin/kill -USR1 $(cat /var/run/nginx.pid 2>/dev/null) 2>/dev/null ||:
    endscript
    
}
```

- `dateext`:文件后缀是日期格式,也就是切割后文件是:xxx.log-20150529(.gz) 这样,如果注释掉,切割出来是按数字递增,即前面说的 xxx.log-1(.gz)
- `monthly`: 日志文件将按月轮循。其它可用值为‘daily’，‘weekly’或者‘yearly’。
- `rotate n`: 一次将存储`n`个归档日志。对于第`n=1`个归档，时间最久的归档将被删除。
- `compress`: 在轮循任务完成后，已轮循的归档将使用`gzip`进行压缩。相反的命令是`nocompress`
- `delaycompress`: 总是与`compress`选项一起用，`delaycompress`选项指示logrotate不要将最近的归档压缩，压缩将在下一次轮循周期进行。
- `missingok`: 在日志轮循期间，任何错误将被忽略，例如“文件无法找到”之类的错误。
- `notifempty`: 如果日志文件为空，轮循不会进行。相反命令`ifempty`是缺省值
- `prerotate/endscript`：在转储以前需要执行的命令可以放入这个对，这两个关键字必须单独成行
- `postrotate/endscript`：在转储以后需要执行的命令可以放入这个对，这两个关键字必须单独成行
- `sharedscripts`:所有的日志文件都轮转完毕后统一执行一次脚本
- `olddir`  转储后的日志文件放入指定的目录，必须和当前日志文件在同一个文件系统

---

##操作
路径`/etc/logrotate.conf`为默认的配置路径，配置特定的日志处理，一般放在`/etc/logrotate.d/`下。在`logrotate.conf`中 使用`include path`包含对应路径

要调用为`/etc/lograte.d/`下配置的所有日志调用`logrotate`：
```linux
 logrotate /etc/logrotate.conf 
```

要为某个特定的配置调用`logrotate`
```linux
 logrotate /etc/logrotate.d/log-file 
```

排障过程中的最佳选择是使用`-d`选项以预演方式运行`logrotate`。要进行验证，不用实际轮循任何日志文件，可以模拟演练日志轮循并显示其输出。
```linux
logrotate -d /etc/logrotate.d/log-file
```

即使轮循条件没有满足，我们也可以通过使用`-f`选项来强制logrotate轮循日志文件，`-v`参数提供了详细的输出。

```linux
logrotate -vf /etc/logrotate.d/log-file
```
---

**参考文档：**

[logrotate manual](http://linuxcommand.org/man_pages/logrotate8.html)
[Linux日志文件总管——logrotate](http://linux.cn/article-4126-1.html)
[logrotate日志管理工具](http://www.ttlsa.com/linux/logrotate-log-management-tools/)