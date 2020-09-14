---
title: HomeBrew替换为中科大源
date: 2020-07-21 14:27:54
tags:
    - homebrew
    - mac
---

HomeBrew每次更新包和安装包都非常慢，本来替换为了清华源，结果发现清华源也一样卡顿，于是换成了中科大源，表现相对好了不少。

<!-- more -->

```shell
# 进入 brew 的仓库根目录
cd "$(brew --repo)"

# 修改为中科大的源
git remote set-url origin https://mirrors.ustc.edu.cn/brew.git

```

继续修改 `homebrew-cask`、`homebrew-core`、`homebrew-services` 的远程仓库地址:

```shell
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git

cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
```

修改完仓库地址后，更新一下，加上 `-v`参数可以看到当前跑的进度：

```shell
brew update -v
```

最后修改`homebrew-bottles`的源信息，如果你使用`Bash`，长期更换方案：

```shell
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
```

临时更换方案：

```shell
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles
```

如果使用`zsh`，长期更换方案：

```shell
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```

临时更换方案：

```shell
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles
```
