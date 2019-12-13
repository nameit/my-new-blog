title: windows下的git bash中添加tree
tags: [git]
categories: 前端
---

# 下载tree
[下载地址](https://sourceforge.net/projects/gnuwin32/)
请下载 Binaries 版本。

# 添加tree
将下载文件的 bin/ 目录下的 tree.exe 复制到 c:/program files/git/usr/bin 目录中。

[原文地址](https://blog.csdn.net/mint_ying/article/details/82793911)

# 例子
```
tree -I node_modules -L 2 //忽略node_modules后只显示2级目录
```