title: git项目中用工具直观的对比文件（windows）
tags: git
categories: 前端
---

## 缘由
在git项目下对比修改过的文件我们一般用`git diff`来操作，如果改动不大可以在git bash左侧看到由加减号列出增减的改动内容，但如果改动的内容太多，在git bash上显示众多的改动内容会是一个令人头疼的问题，因为git bash显示的对比过于简单、不直观，因此我们需要外部的工具来帮我们完成直观的对比。

## 配置
git也意识到了这一点，所以我们可以通过.gitconfig文件来配置我们需要的外部对比工具（比如winMerge）。
文件（一般直接在C盘user里你的名字文件夹下）可以配置如下：
```
[mergetool]
    prompt = false
    keepBackup = false
    keepTemporaries = false
 
[merge]
    tool = winmerge
 
[mergetool "winmerge"]
    name = WinMerge
    trustExitCode = true
    cmd = "/c/Program\\ Files\\ \\(x86\\)/WinMerge/WinMergeU.exe" -u -e -dl \"Local\" -dr \"Remote\" $LOCAL $REMOTE $MERGED
 
[diff]
    tool = winmerge
 
[difftool "winmerge"]
    name = WinMerge
    trustExitCode = true
    cmd = "/c/Program\\ Files\\ \\(x86\\)/WinMerge/WinMergeU.exe" -u -e $LOCAL $REMOTE
```
里面的具体参数请参看[git官方说明](http://git-scm.com/docs/git-config)。

## 调用
当你再次遇上需要对比或合并时，就可以运行`git difftool <file>` 或 `git mergetool <file>` 来自动打开对比工具进行直观的对比。
-- just enjoy it--
![图片](http://www.edowning.net/pic/20083314532471369.png)
