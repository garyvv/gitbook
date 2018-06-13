#GITHUB/GITLAB添加多个SSH KEY
> 系统：macOS X

生成ssh key的代码如下：
- 生成key：
```
$ ssh-keygen -t rsa -C “youremail@yourcompany.com”
```
(回车，This creates a new ssh key, using the provided email as a label.)
- 上一步回车后。会弹出询问你保存ssh key的文件存放在哪里（默认是/Users/you/.ssh/id_rsa，如果不想切换地址，那就直接回车）
- 上一步回车之后，会让你输入一个secure passphrase(可以直接回车):
- 完成之后，到～/.ssh下可以查看到id_rsa和id_rsa.pub，将id_rsa.pub里的key拷出来，添加到你的gitlab账户里就行了。
---
接着在github.com网站上再生成一个ssh key,生成过程与上一次唯一不同的地方在于保存ssh key的文件要换一下，假如上一次是/Users/you/.ssh/id_rsa,那么这次就可以切换成/Users/you/.ssh/id_rsa_github，相应的也会生成一个id_rsa_github.pub；

### 注意：
因为SSH默认只读取id_rsa,为了让SSH识别新的私钥,需要使用命令将其添加到SSH agent,命令如下：
```
　　$ssh-add ~/.ssh/id_rsa
　　$ssh-add ~/.ssh/id_rsa_github
```
若执行ssh-add时提示“Could not open a connection to your authentication agent”,则执行下面的命令：
```
　  $ssh-agent bash
```
然后再运行ssh-add命令(可以通过ssh-add -l查看私钥列表)；
接着修改配置文件：
在~./ssh目录下新建一个config文件,命令如下：
```    
    touch config
```
编辑config文件，往里添加内容如下：
```
　　# gitlab
　　Host gitlab.com
　　     HostName gitlab.com
        PreferredAuthentications publickey
        IdentityFile ~/.ssh/id_rsa
　　# GitHub
　　Host github.com
        HostName github.com
  　　   PreferredAuthentications publickey
　　　　　IdentityFile ~/.ssh/id_rsa_github
```
最后测试：
```
　　　　$ssh -T git@github.com
　　　　输出：Hi user! You've successfully authenticated, but 
```
GitHub does not provide shell access. 就表示成功的连上github了
 
 