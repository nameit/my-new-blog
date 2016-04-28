title: windows自定义bash的alias
tags: [bash,git]
---

### 缘由
又来装逼了，看到同事的mac上可以简单敲gco就能切换分支，而自己在git bash里只能用敲`git checkout`， 麻烦！

### 方法
其实git bash支持自定义alias，如下两种方法 ：
> 1、在C盘用户名下的.gitconfig里可以输入[alias] co = checkout
> 2、用命令 `git config --global alias.co checkout`

但这两种方法实际用的时候只能这样省略：`git co`， 前面还有git没有简写。  

### bash 方法
后来想到git bash也是bash，于是用命令`alias gs="git status"` 终于可以实现，莫急，窗口关闭后再打开又没了，需要重新设置。麻烦！

### 改进
于是google了一下搜到可以在c盘自己用户名下建一个.bashrc文件专门用于存放自己的alias，类似：```
alias gs="git status"    
alias gco="git checkout"  
alias gada="git add --all"```

然后还需在旁边加一个 .bash_profile 文件,文件中写入内容 `source ~/.bashrc`，
重开窗口输入`gco master` 成功切换到master分支！