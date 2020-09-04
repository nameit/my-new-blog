title: 打开next主题下的tags、分类模式

tags: [nodejs]

---

首先打开主题下(比如next)的配置文件_config.yml，然后搜索menu找到如下配置项，将about、tags、categories前的#号去掉，就开启了关于、标签和分类标签，当然还有其他菜单项也可以开启

```
menu:
  home: / || home
  about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

重新生成部署后，可以看到新增的菜单项，但是单击后会报如下错误

```
Cannot GET /about/
Cannot GET /tags/
Cannot GET /categories/
```

这是因为你还需运行如下命令新建相关页面

```
hexo new page "about"
hexo new page "tags"
hexo new page "categories"
```

运行结果如下，会再source文件下创建about、tags、categories文件夹，每个文件夹下还会创建一个index.md文件表示关于、标签页分类页面，编辑这三个MarkDown文件可以自定义这三个页面的内容

```
D:\hexo\blog>hexo new page "about"

INFO  Created: D:\hexo\blog\source\about\index.md
D:\hexo\blog>hexo new page "tags"
INFO  Created: D:\hexo\blog\source\tags\index.md
D:\hexo\blog>hexo new page "categories"
INFO  Created: D:\hexo\blog\source\categories\index.md
```

还差最后一步，打开各页面对应的index.md文件，编辑如下内容，title和date是默认生成的，增加type即可

```
---
title: about
date: 2019-06-25 19:16:17
type: "about"
---

---
title: about
date: 2019-06-25 19:16:17
type: "tags"
---

```

重新生成和部署即可看到效果