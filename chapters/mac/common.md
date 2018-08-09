# Mac OS 常用软件

<extoc></extoc>

## HomeBrew

[HomeBrew](https://brew.sh/) 是一款Mac OS平台下的软件包管理工具，拥有安装、卸载、更新、查看、搜索等很多实用的功能。简单的一条指令，就可以实现包管理，而不用你关心各种依赖和文件路径的情况，十分方便快捷。

### 安装

```bash
> /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### 基本使用

- 安装任意包

```bash
brew install <packageName>
# 安装图形化app
brew cask install <packageName>

# 示例：安装wget
brew install wget
```

- 卸载任意包

```bash
brew uninstall <packageName>

# 示例：卸载git
brew uninstall git
```

- 查询可用包

```bash
brew search <packageName>
```

- 查看已安装包列表

```bash
brew list
```

- 查看任意包信息

```bash
brew info <packageName>
```

- 更新Homebrew

```bash
brew update
```

- 更新某软件

```bash
brew upgrade <packageName>

# 例如更新 git
brew upgrade git
```

### 替换国内源


- Homebrew Cask

```bash
# 替换为 USTC 镜像：

cd "$(brew --repo)"/Library/Taps/caskroom/homebrew-cask
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git

# 重置为官方地址：

cd "$(brew --repo)"/Library/Taps/caskroom/homebrew-cask
git remote set-url origin https://github.com/caskroom/homebrew-cask
```

- Homebrew Core

```bash
# 替换 USTC 镜像：

cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

# 重置为官方地址：

cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://github.com/Homebrew/homebrew-core
```

- Homebrew

```bash
# 替换USTC镜像：
cd "$(brew --repo)"
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
git remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

# 重置为官方地址：

cd "$(brew --repo)"
git remote set-url origin https://github.com/Homebrew/brew.git
```

### 常用软件

```
# sourcetree
brew cask install sourcetree

# sublime text
brew cask install sublime-text

# vscode
brew cask install visual-studio-code

# google-chrome
brew cask install google-chrome

brew cask install phpstorm

# iterm2 
# 配置说明 https://www.cnblogs.com/xishuai/p/mac-iterm2.html
brew cask install iterm2

# 搜狗输入法
brew cask install sogouinput

# 屏保安装屏保程序 Fliqlo
brew cask install fliqlo 

# cheatsheet
brew cask install cheatsheet

```

---
