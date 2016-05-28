title: 利用 Expres+jade+stylus+nodemon+browserSync 构建高效前端环境
date: 2016-05-27 16:25:51
tags: [前端环境]
---

# 起因
随着公司业务的极具增长，赶进度式的开发要求前端人员能更高效的构建自己的开发环境。
项目跑了很多，但从各方面来看总是不够快。

# 为什么是它们
跑项目总要起服务，从最初的apache到connect中间件以及现在的browser-sync，browser-sync的实时、快速响应文件的更改并自动刷新页面，移动端还能真机调试是我选择他的原因。
Express，本想用它来起服务的，但有了browserSync似乎就多余了，但路由控制和中间件的使用总少不了它，比起之前先生成静态html文件再起服务，express要快不只一点点。
jade，额……目前改名为pug。喜欢其缩进式的表达方式---简洁、清晰，传参和数据导入都有，作为模板元老，管理也够用了，而且express默认的模板引擎也是它。
stylus,css的预编译器。喜欢他也是因为其简洁缩进式的风格，不需要冒号、分号和括号，好处就是如果你要在缩进式的中间添加一个类名，直接添加即可，不需要再去找对应结束的大括号，而且sass有的功能他几乎都有，当然它也提供了中间件的支持和自带sourcemaps，相对于sass比起来，sytlus更轻巧，对node更有亲和力，对于我这样一个懒前端来说不需要安装ruby之类的外部语言是最好不过了。
既然采用路由的方式来构建，我们最终能在浏览器端看到文件的改变需要重启app应用，nodemon这个轻量级的自动重启工具就必不可少。

# 项目目录
```
.
├── dist
│   ├── css
│   ├── images
│   ├── js
│   └── temp
├── docs       // 项目的相关文档
├── gulpfile.js  // 运行dist用到的插件
├── jade
│   ├── _includes    // 用于include的部分
│   └── _layout   // 用于继承的模板
├── package.json  // 依赖包
├── public    // 静态文件目录
│   ├── css
│   ├── images
│   ├── js
│   └── temp
├── README.md
├── server.js  // 主app入口
└── stylus
    ├── _components  // 组件样式
    ├── _config    // 样式配置
    └── css       // 主文件
```
# 项目注意事项

为了方便配置路由，package.json中引入了glob这个依赖，有了它我们就可以找到并筛选想要的文件，以便我们对路由的统一配置。

最后为了方便开发获取代码，生成dist文件夹推入dist的分支。

