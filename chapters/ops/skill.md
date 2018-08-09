# 奇淫巧技

- 文件占用空间
> 列出当前文件夹的占用
```shell
du -sh ./*
```

- 根据进程号找到可执行文件的路径
```shell
ls -l /proc/<pid>/exe
```

- shell 脚本统计当前并发连接数
```shell
netstat -pnt | grep :80 | grep ESTABLISHED | wc -l
```