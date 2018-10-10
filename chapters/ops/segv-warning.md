如何找出发生SEGV内存错误的程序
=================

有用户使用《[lnmp一键安装包](//blog.linuxeye.com/31.html)》在CentOS6.6 64bit上安装php（php5.4、[Opcache](https://blog.linuxeye.cn/361.html)、ZendGuardLoader）、hhvm，用vhost.sh添加虚拟主机选择php，使用某主题（主题加密，需要使用ZendGuardLoader），启用改主题直接报502错误。束手无策，于是找我来排错，如下：

通过查看php日志/usr/local/php/var/log/php-fpm.log，有如下警告信息：

```
1.  \[16-Mar-2015 16:03:09\] WARNING: \[pool www\] child 9453 exited on signal 11 (SIGSEGV) after 9.601040 seconds from start
```

日志中的信息表明，进程号为9453的进程由于收到SIGSEGV信号而退出了。收到这个信号的时候，程序是可以生成core文件的。不过通过日志我们可以知道进程9453退出时没有生成core文件。因为在php-fpm的日志中，如果退出时生成了core文件，日志中会有“SIGSEGV – core dumped”字样。如：

```
1.  \[16-Mar-2015 16:04:29\] WARNING: \[pool www\] child 9581 exited on signal 11 (SIGSEGV - core dumped) after 15.921916 seconds from start
```

> core dump文件对于诊断Linux中程序的问题非常有用。当程序异常退出的时候，可能会生成core文件。如，程序写一个不属于他的内存，操作系统出于保护，会发信号给程序，程序可能会因此而退出，退出的时候可能会生成core文件。我们可以通过分析core文件，找出程序中那里有内存问题。这篇文章主要是阐述生成core文件需要做的一些设置。


**如何生成core文件**

默认Linux操作系统是不允许生成core文件的。如下：

```
1.  \[root@linuxeye ~\]# ulimit -a
2.  core file size          (blocks, -c) 0
3.  data seg size           (kbytes, -d) unlimited
4.  scheduling priority             (-e) 0
5.  file size               (blocks, -f) unlimited
6.  pending signals                 (-i) 5762
7.  max locked memory       (kbytes, -l) 64
8.  max memory size         (kbytes, -m) unlimited
9.  open files                      (-n) 65535
10.  pipe size            (512 bytes, -p) 8
11.  POSIX message queues     (bytes, -q) 819200
12.  real-time priority              (-r) 0
13.  stack size              (kbytes, -s) 10240
14.  cpu time               (seconds, -t) unlimited
15.  max user processes              (-u) 65535
16.  virtual memory          (kbytes, -v) unlimited
17.  file locks                      (-x) unlimited
```

可以通过如下命令解除限制：

```
1.  \[root@linuxeye ~\]# ulimit -c unlimited
2.  \[root@linuxeye ~\]# ulimit -a
3.  core file size          (blocks, -c) unlimited
4.  data seg size           (kbytes, -d) unlimited
5.  scheduling priority             (-e) 0
6.  file size               (blocks, -f) unlimited
7.  pending signals                 (-i) 5762
8.  max locked memory       (kbytes, -l) 64
9.  max memory size         (kbytes, -m) unlimited
10.  open files                      (-n) 65535
11.  pipe size            (512 bytes, -p) 8
12.  POSIX message queues     (bytes, -q) 819200
13.  real-time priority              (-r) 0
14.  stack size              (kbytes, -s) 10240
15.  cpu time               (seconds, -t) unlimited
16.  max user processes              (-u) 65535
17.  virtual memory          (kbytes, -v) unlimited
18.  file locks                      (-x) unlimited
```

注意，ulimit -c 的设置仅仅是对你完成设置后启动的进程有效。而且退出登陆后，再进入需要从新设置。否则从新登陆后启动的进程也无法生成core文件。

如果想永久生效，可以把命令加入到 /etc/profile 中。建议不要这样做, 会疯狂dump文件，浪费性能

**如何找到core文件**

一般情况下，core文件会生成在你执行程序的地方。文件名是core.进程号

你也可以指定core文件名和生成目录。在/etc/sysctl.conf 文件中指定

1.  vi /etc/sysctl.conf
2.  kernel.core\_uses\_pid = 1 #追加进程号到core文件名中
3.  fs.suid_dumpable = 2 #确保设置属主的进程也可以生成core文件
4.  kernel.core_pattern = /tmp/core-%e-%s-%u-%g-%p-%t #指定core文件生成的位置和文件名规则。

文件名规则可以使用的参数有：

1.  %% – 符号%
2.  %p – 进程号
3.  %u – 进程用户id
4.  %g – 进程用户组id
5.  %s – 生成core文件时收到的信号
6.  %t – 生成core文件的 时间 (seconds since 0:00h, 1 Jan 1970)
7.  %h – 主机名
8.  %e – 程序文件名

执行如下命令，让设置生效

1.   sysctl -p

重启php-fpm

```
service php-fpm restart
``` 

重现502错误

访问http://demo.linuxeye.com/wp-admin/customize.php?theme=dux

日志/usr/local/php/var/log/php-fpm.log中会有"SIGSEGV – core dumped"字样

**如何使用core文件**

可以使用[gdb命令](https://blog.linuxeye.cn/wp-content/themes/begin/inc/go.php?url=http://www.linuxeye.com/Linux/gdb.html)查看core文件信息

>  gdb -e /usr/local/php/sbin/php-fpm -c /tmp/core-php-fpm-11-501-501-9581-1426493066

[![](/wp-content/uploads/2015/03/gdb.gif)](https://blog.linuxeye.cn/wp-content/uploads/2015/03/gdb.gif)

根据上面的堆栈信息，可以知道Optimizer（即Opcache）有问题，编辑/usr/local/php/etc/php.ini，注销掉Opcache段，重启php-fpm ，正常。。。

参考：http://www.searchtb.com/2014/03/如何找出发生segv内存错误的程序.html


