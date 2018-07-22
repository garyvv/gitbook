# Git相关
- 使用brew安装git和bash-compeletion
```
    $ brew install git
    $ brew install bash-completion
```
    
- 配置bash-completion到环境变量
> 在~/.bash_profile中添加

```
    # bash-completion
    if [ -f $(brew --prefix)/etc/bash_completion ]; then
        source $(brew --prefix)/etc/bash_completion
    fi
```
