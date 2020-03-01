---
title: Powerlevel10k:Instant Prompt模式
date: 2020-03-01 16:08:50
tags:
    - zsh
    - p10k
    - shell
    - dotfiles
---

最近在折腾我的`zsh`配置，`oh-my-zsh`、`prezto`、`zinit`等框架都折腾了个遍。以后大概会使用`zinit`为主体，混合搭配`oh-my-zsh`和`prezto`的插件来使用。其实我现在比较在意shell的反应速度，`oh-my-zsh`实在是有些臃肿（功能多插件多无解），`prezto`虽然优化了启动速度，但在功能性上不能让我满意（其实也是速度不够快orz)。

<!-- more -->

在研究这些`zsh`框架的时候，我读到这么一篇文章：[Comparison of ZSH frameworks and plugin managers][1]，作者测试了各个框架的性能表现，得出来的结论是[`zplugin`][2]的速度是最快的，而且领先优势非常大。作者后来将[`zplugin`][3]改名为[`zinit`][4]，我们后面统一用[`zinit`][5]来称呼。

## 使用感受

在[`zinit`][6]的实际使用过程中，可以感觉到[`zinit`][7]速度上确实有所提升，但类似于我使用`prezto`的体验，总感觉还是不够快。大致有以下使用感受：

 1. 插件加载管理的性能和`prezto`类似，都要比`oh-my-zsh`更好，但也就这样了。
 2. 插件机制非常强大，可以无缝使用`prezto`和`oh-my-zsh`的插件，灵活性非常好，配合延迟加载机制，整个插件系统的编排、拓展能力非常强力。
 3. `zinit`的其他高级特性我还没研究明白，我建议大家可以参考[加速你的 zsh —— 最强 zsh 插件管理器 zplugin/zinit 教程 ][8]来配置自己的`zinit`，当然[官方文档][9]也很不错。

在折腾这些框架的几天里，我发现`zsh`的性能其实由两方面决定，首先是前面讲到的插件数量和加载策略，这方面`zinit`和`prezto`已经做的很不错了。而另一方面就是`pyenv`和`nvm`这种包管理器插件非常影响性能，这也是为什么`zinit`和`prezto`不能让我满意的原因。

这是我的`.bash_profile`里面的内容：

``` shell
PATH="/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Applications/VMware Fusion.app/Contents/Public:/usr/local/aria2/bin:/Users/unicorn/.zinit/polaris/bin:/Users/unicorn/.nvm/versions/node/v11.3.0/bin:/Users/unicorn/sdk:/Users/unicorn/sdk/platform-tools:/Users/unicorn/sdk/tools:/Users/unicorn/maven/bin:/Users/unicorn/.rvm/bin"; export PATH;
MANPATH="/usr/share/man:/usr/local/share/man:/usr/local/aria2/share/doc/man:/Users/unicorn/.nvm/versions/node/v11.3.0/share/man:/usr/local/aria2/share/man:/Users/unicorn/.rvm/man:/Library/Developer/CommandLineTools/usr/share/man"; export MANPATH;
ANDROID_HOME=$HOME/sdk
GRADLE_USER_HOME=$HOME/.gradle
export GRADLE_USER_HOME
export ANDROID_HOME
export PATH=$PATH:$ANDROID_HOME
export PATH=$PATH:$ANDROID_HOME/platform-tools
export PATH=$PATH:$ANDROID_HOME/tools

export M2_HOME=$HOME/maven
export M2=$M2_HOME/bin
#export MAVEN_OPTS=-Xmx:1024M
#export JAVA_TOOL_OPTIONS=-Xmx2028M
export PATH=$PATH:$M2

# Add RVM to PATH for scripting. Make sure this is the last PATH variable change.
export PATH="$PATH:$HOME/.rvm/bin"
export NVM_DIR="/Users/unicorn/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm" # Load RVM into a shell session *as a function*

export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles

if [ -f ~/.bashrc ]; then
   source ~/.bashrc
fi
```

可以直接参考P10K作者的demo，就能知道这些玩意对性能的影响:
![raw .zshrc with lag][10]

## powerlevel10k

orz前面废话太多了，终于到正题了。[powerlevel10k][11]是[romkatv][12]为`zsh`开发的主题；其实称作主题还挺奇怪的，`powerlevel10k`有着普通主题做不到的魔力，它主打速度、灵活性和开箱即用的体验。以后可能会单独研究一下这个主题，但今天我只想讲讲它的[Instant prompt][13]模式。

### Instant prompt

我们再来重放一下之前的lag表现：
![raw .zshrc with lag][14]

开启Instant prompt之后：
![.zshrc with instant prompt][15]

说实话当我看到这个对比时确实惊到了，通过在`shell`加载过程中提供一个简化的`prompt`，Instant prompt通过后台加载机制大幅度降低了启动延迟，对于我这种强迫症患者真是太棒了（反正我输入第一条指令也需要至少2-3s的时间，那时候后台加载过程早就结束了）。

### 开启Instant prompt遇到的问题

根据作者给出的指南[How do I enable instant prompt?][16]，绝大多数情况下只要将这段代码加入到`.zshrc`文件的开头即可：

``` shell
# Enable Powerlevel10k instant prompt. Should stay close to the top of ~/.zshrc.
# Initialization code that may require console input (password prompts, [y/n]
# confirmations, etc.) must go above this block; everything else may go below.
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi
```

比较诡异的是我这么做之后发现打开速度还是很慢，于是开始了逐项排查。

#### zinit加载方式

因为我使用`zinit`来加载`p10k`主题，理所当然的我开始排查`zinit`。

`zinit`加载：

``` shell
zinit ice depth=1; zinit light romkatv/powerlevel10k
```
我换成了这样：

``` shell
source ~/.zinit/plugins/romkatv---powerlevel10k/powerlevel10k.zsh-theme
```

但实测结果是和`zinit`无关，看来不是`zinit`的锅。

#### instant prompt enable检查

在开启instant prompt的时候，根据作者的指引，我们在`.zshrc`的开头加入了

``` shell
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi
```

其中的`${XDG_CACHE_HOME:-$HOME/.cache`路径其实就是`~/.cache`，但我并没有发现`p10k-instant-prompt-unicorn.zsh`文件：
![cache dir empty][17]

难道是没有开启成功吗？于是我又执行了一次`p10k configure`指令重新配置`p10k`，根据帮助文档，执行`p10k configure`会自动开启`instant prompt`模式。实测多次后还是没有生成`instant-prompt`文件。

正在我一筹莫展的时候，我看到了作者在[reddit][18]上的讨论，上面有位用户的留言和我的状况很类似：

> Just updated to the latest version, but seems nothing changed.
[[ -r "\${XDG_CACHE_HOME:-\$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]
returns false since theres no instant-prompt file in ~/.cache
Any idea what might be the problem?
I'm no macOS Majave.
Thanks in advance!

而`p10k`的作者回复：

> What's the output of echo $ZSH_VERSION? If it's less than 5.4, this is the expected behavior. From the docs:
NOTE: Instant prompt requires ZSH >= 5.4. It's OK to enable it even when using an older ZSH version but it won't do anything.

看到这里我才恍然大悟，这个特性需要高版本的`zsh`支持，至少要在5.4之上。以后应该养成仔细阅读readme文档的习惯。

#### 升级zsh版本遇到的问题

`mac`内置的`zsh`并非是最新版本：

![zsh-5.3][19]

既然zsh版本升级就能解决这个问题，于是我直接升级了zsh版本：

``` shell
brew install zsh
```

![zsh-update][20]

完事大吉，重新打开`iterm2`，结果速度还是很慢，一看`.cache`目录，比之前多了一些文件，但依然没有`instant-prompt`文件：

![no-instant-prompt-files][21]

这时候我又去仔细看作者的回复，发现我又少看东西了：

> Keep in mind that the output of echo $ZSH_VERSION can be different from zsh --version. You need to use the former.

好吧，原来`brew install zsh`并不会成为默认的`zsh`，而且`echo $ZSH_VERSION`和`zsh --version`的version也不一致:

![different-of-zsh-version][22]

参考[How do I update zsh to the latest version?][23]，切换到升级后的zsh版本，重新打开终端，重新进行`p10k configure`后，`Instant Prompt`模式设置项终于出来了：

![p10k-configure-instant-prompt][24]

最终效果：

![quick-launch-zsh][25]

真的是非常快...

  [1]: https://gist.github.com/laggardkernel/4a4c4986ccdcaf47b91e8227f9868dedx
  [2]: https://zdharma.org/zinit/wiki/
  [3]: https://zdharma.org/zinit/wiki/
  [4]: https://zdharma.org/zinit/wiki/
  [5]: https://zdharma.org/zinit/wiki/
  [6]: https://zdharma.org/zinit/wiki/
  [7]: https://zdharma.org/zinit/wiki/
  [8]: https://www.aloxaf.com/2019/11/zplugin_tutorial/
  [9]: https://zdharma.org/zinit/wiki/
  [10]: https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/no-instant-prompt.gif
  [11]: https://github.com/romkatv/powerlevel10k
  [12]: https://github.com/romkatv
  [13]: https://github.com/romkatv/powerlevel10k/blob/master/README.md#instant-prompt
  [14]: https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/no-instant-prompt.gif
  [15]: https://raw.githubusercontent.com/romkatv/powerlevel10k-media/master/instant-prompt.gif
  [16]: https://github.com/romkatv/powerlevel10k/blob/master/README.md#how-do-i-enable-instant-prompt
  [17]: https://cdn.jsdelivr.net/gh/londbell/pic/img/Instant-Prompt-Cache-Dir-Empty.png
  [18]: https://www.reddit.com/r/zsh/comments/dk53ow/new_powerlevel10k_feature_instant_prompt/
  [19]: https://cdn.jsdelivr.net/gh/londbell/pic/img/zsh-5.3.png
  [20]: https://cdn.jsdelivr.net/gh/londbell/pic/img/zsh-update.png
  [21]: https://cdn.jsdelivr.net/gh/londbell/pic/img/no-instant-prompt-files.png
  [22]: https://cdn.jsdelivr.net/gh/londbell/pic/img/different-of-zsh-version.png
  [23]: https://stackoverflow.com/questions/17648621/how-do-i-update-zsh-to-the-latest-version.png
  [24]: https://cdn.jsdelivr.net/gh/londbell/pic/img/p10k-configure-instant-prompt.png
  [25]: https://cdn.jsdelivr.net/gh/londbell/pic/img/quick-launch-zsh.gif