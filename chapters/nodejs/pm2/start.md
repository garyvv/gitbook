# 开机自启动
- 设置
```
    sudo pm2 start ./bin/www

    sudo pm2 save

    sudo pm2 startup

    sudo pm2 save
```
- 重启系统试一下：如果不行执行
```
    chattr +i /home/XXX/.pm2/dump.pm2再重试一下
```
