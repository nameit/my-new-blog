# 步骤

## 下载

clone下来后，

```cnpm i```，

下载依赖。

## 安装主题

一般来说hexo server就能在本地预览了。

但之前下载了next主题，所以需要在github自己的fork里下载hexo-theme-next主题。进到自己项目theme目录下  

```git clone git@github.com:nameit/hexo-theme-next.git themes/next。```

只有下载了这个主题，再hexo generate才能生成public文件夹里面的css、images文件夹等。

> 如果没有全局安装hexo，可以这样引用： ./node_modules/hexo/bin/hexo

## 起服务

generate后就可以通过

```./node_modules/hexo/bin/hexo server -p 4004```

 本地浏览了（有时4000端口占用可以通过-p加端口的形式另起一个端口）

具体写作参见[官网](https://hexo.io/zh-cn/docs/writing)

## 遇到的问题

1、fatal: unable to access 'https://github.com/nameit/nameit.github.io.git/': OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443

[解决办法](https://blog.csdn.net/daerzei/article/details/79528153): ```git config --global --unset http.proxy```

2、Error: fatal: in unpopulated submodule '.deploy_git'

解决方法： 把.depoy_git删除，重新运行

```hexo g```

```hexo d```