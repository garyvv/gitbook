# Charles安装证书抓https

[官方教程](https://link.jianshu.com?t=https%3A%2F%2Fwww.charlesproxy.com%2Fdocumentation%2Fusing-charles%2Fssl-certificates%2F)

### Mac

1.  点击 Charles菜单下 `Help -> SSL Proxying -> Install Charles Root Certifacate` 选择添加。
2.  从应用`钥匙串访问`搜索Charles，找到添加的证书，双击证书，在`信任`下选择`始终信任`。

### IOS

1.  从[chls.pro/ssh](https://link.jianshu.com?t=http%3A%2F%2Fchls.pro%2Fssl)下载证书到本地。
2.  以AirDrop方式共享或者邮件附件发送到IOS手机上，并在手机上选择安装。
3.  在手机`设置 -> 通用 -> 关于本机 -> 证书信任设置` 中打开信任。

### Android

1.  从 [chls.pro/ssh](https://link.jianshu.com?t=http%3A%2F%2Fchls.pro%2Fssl) 下载证书到本地。
2.  复制到手机sdcard上
3.  在手机`设置 -> 安全 -> 从存储设备安装` 中命名并选择WLAN按照。

**注意**：通过Charles抓包的时候需要在菜单`Proxy -> SSL Proxying`中配置需要开启`Enable SSL Proxying`并配置需要抓包的域名。