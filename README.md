# 步骤

## 下载

clone下来后，cnpm i，下载依赖。

## 安装主题

一般来说hexo server -s就能在本地预览了。

但之前下载了next主题，所以需要在github自己的fork里下载hexo-theme-next主题。进到自己项目目录下```git clone git@github.com:nameit/hexo-theme-next.git themes/next。```

只有下载了这个主题，再hexo generate才能生成public文件夹里面的css、images文件夹等。

> 如果没有全局安装hexo，可以这样引用： ./node_modules/hexo/bin/hexo

## 起服务

generate后就可以通过```./node_modules/hexo/bin/hexo server -p 4004```  本地浏览了（有时4000端口占用可以通过-p加端口的形式另起一个端口）

具体写作参见[官网](https://hexo.io/zh-cn/docs/writing)