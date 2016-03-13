title: 修改Git默认编辑器
tags: [sublime,git]
---


### 缘由
最近用git commit --amend的时候自动跳出来的vim编辑器内输入“文件”的“文”老是不显示，于是怒下决心要把vim编辑器换成sublime编辑器。

### 配置
还好git和sublime text 3(Build 3065以上)支持了此功能，只需要敲入命令：
```
git config --global core.editor "'D:/program files/sublime text 3/subl.exe' -w"
```
即可。
或
在.gitconfig文件里设置：
```
[core]
    editor = 'D:/Program Files/Sublime Text 3/subl.exe' -w
```

### 注意事项
> * setting 里的`hot_exit`需设置为 true
> * setting 里的`remember_open_files`需设置为 true
> * 保存并关闭（CTRL+w）自动跳出来的tab页时才能起保存作用

![sublime-editor](/images/sublime-editor.png)

参考[stackoverflow](http://stackoverflow.com/questions/8951275/how-can-i-make-sublime-text-the-default-editor-for-git)