
title: pacakge.json部分知识理解
tags: [nodejs]
---


### Version

软件版本号有四部分组成：

- 第一部分为主版本号，变化了表示有了一个`不兼容上个版本的大更改`。
- 第二部分为次版本号，变化了表示`增加了新功能，并且可以向后兼容`。
- 第三部分为修订版本号，变化了表示`有bug修复，并且可以向后兼容`。
- 第四部分为日期版本号加希腊字母版本号，希腊字母版本号共有五种，分别为base、alpha、beta 、RC 、 release
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190118111447199.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDgxNzExNQ==,size_16,color_FFFFFF,t_70)

```js
{
  "dependencies": {
			"foo": "2.1.0", // 指定版本
      "bar": "~1.2.3", // 锁定主要版本和次要版本，只能改变最后一位且大于等于当前值
      "elf": "^2.1.2" // 主要版本非0状态下锁定大版本，次要版本和小版本可以变动为大于等于当前值，主要版本为0状态下，锁定主要版本和次要版本，同~
    	"thr": "latest" // 最新版本
  	}
}
```



### License

![img](https://ruanyifeng.com/blogimg/asset/201105/bg2011050101.png)

### 依赖包

**dependencies** : 业务依赖

**devDependencies** ：开发依赖 

**peerDependencies** : 同等/同伴依赖

要求安装者安装本插件包时，指定其他依赖的版本号，但只是警告⚠️，并非强制给你安装，需要自行安装指定版本，用来解决插件与依赖的包不一致。

比如安装element-ui插件包时，它会指定vue的版本号，如果你安装的vue版本号不在它指定的范围内它会警告你并提示你手动安装它指定的版本。

**bundledDependencies** : 打包依赖

`npm pack` 用来生成一个包含非node_modules的gtz压缩文件，如果要加上node_modules，可以在`bundledDependencies`属性里把dependencies和devDependencies已有的依赖放入数组中：

```js

"name": "font-end",
"version": "1.0.0",
"dependencies": {
	"fe1": "^0.3.2",
	...
 },
"devDependencies": {
	...
	"fe2": "^1.0.0"
 },
"bundledDependencies": [
	"fe1",
	"fe2"
 ]
}
```



### package-lock.json

- 在⼤版本相同的前提下，如果⼀个模块在 package.json 中的⼩版本要⼤于 package-lock.json 中的⼩版本，则在执⾏ npminstall 时，会将该模块更新到⼤版本下的最新的版本，并将版本号更新⾄ package-lock.json 。如果⼩于，则被package-lock.json 中的版本锁定。

- 如果⼀个模块在 package.json 和 package-lock.json 中的⼤版本不相同，则在执⾏ npm install 时，都将根据 package.json 中⼤版本下的最新版本进⾏更新，并将版本号更新⾄ package-lock.json 。

- 如果⼀个模块在 package.json 中有记录，⽽在 package-lock.json 中⽆记录，执⾏ npm install 后，则会在 package-lock.json ⽣成该模块的详细记录。同理，⼀个模块在 package.json 中⽆记录，⽽在 package-lock.json 中有记录，执⾏ npm install 后，则会在 package-lock.json 删除该模块的详细记录。



### bin

它是一个命令名和本地文件名的映射。在安装时，如果是全局安装，npm将会使用符号链接把这些文件链接到prefix/bin，如果是本地安装，会链接到./node_modules/.bin/。

通俗点理解就是我们全局安装， 我们就可以在命令行中执行这个文件， 本地安装我们可以在当前工程目录的命令行中执行该文件。

```js
"bin": {
  "mason": "./index.js"
},
```

要注意： 这个index.js文件的头部必须有这个`#!/usr/bin/env node`节点， 否则脚本将在没有节点可执行文件的情况下启动。

Index.js文件：

```js
#!/usr/bin/env node

console.log('cool')
```



全局`npm i `后接下来你在任意目录新开一个命令行， 输入`mason`， 你将看到`cool`字段。

vue-cli和create-react-app都是这个道理。



### script

npm 脚本的原理非常简单。每当执行npm run，就会自动新建一个 Shell，在这个 Shell 里面执行指定的脚本命令。因此，只要是 Shell（一般是 Bash）可以运行的命令，就可以写在 npm 脚本里面。

比较特别的是，npm run新建的这个 Shell，会将当前目录的node_modules/.bin子目录加入PATH变量，执行结束后，再将PATH变量恢复原样。

这意味着，当前目录的node_modules/.bin子目录里面的所有脚本，都可以直接用脚本名调用，而不必加上路径。比如，当前项目的依赖里面有 Mocha，只要直接写mocha test就可以了。

- 通配符：`*`表示任意文件名，`**`表示任意一层子目录。

  ```js
  "lint": "jshint *.js"
  "lint": "jshint **/*.js"
  ```

如果要将通配符传入原始命令，防止被 Shell 转义，要将星号转义。

   ```js
   "test": "tap test/\*.js"
   ```

- 脚本传参符号： --

  ```js
  "server": "webpack-dev-server --mode=development --open --iframe=true "
  ```

- 脚本执行顺序

  并行执行（即同时的平行执行），可以使用`&`符号

  ```js
  npm run script1.js & npm run script2.js
  ```

  继发执行（即只有前一个任务成功，才执行下一个任务），可以使用`&&`符号

  ```js
  $ npm run script1.js && npm run script2.js
  ```

- 脚本钩子

  npm 脚本有pre和post两个钩子。举例来说，build脚本命令的钩子就是prebuild和postbuild。

  ```
  "prebuild": "echo I run before the build script",
  "build": "cross-env NODE_ENV=production webpack",
"postbuild": "echo I run after the build script"
  ```

  用户执行npm run build的时候，会自动按照下面的顺序执行。

  ```
  npm run prebuild && npm run build && npm run postbuild
  ```
  
  因此，可以在这两个钩子里面，完成一些准备工作和清理工作。下面是一个例子。
  
  ```
  "clean": "rimraf ./dist && mkdir dist",
  "prebuild": "npm run clean",
  "build": "cross-env NODE_ENV=production webpack"
  ```
  
  
  
  npm 默认提供下面这些钩子。
  
  - prepublish，postpublish
  - preinstall，postinstall
  - preuninstall，postuninstall
  - preversion，postversion
  - pretest，posttest
  - prestop，poststop
  - prestart，poststart
  - prerestart，postrestart
  
  自定义的脚本命令也可以加上pre和post钩子。比如，myscript这个脚本命令，也有premyscript和postmyscript钩子。不过，双重的pre和post无效，比如prepretest和postposttest是无效的。
  
  npm 提供一个npm_lifecycle_event变量，返回当前正在运行的脚本名称，比如pretest、test、posttest等等。所以，可以利用这个变量，在同一个脚本文件里面，为不同的npm scripts命令编写代码。请看下面的例子。
  
  ```js
  const TARGET = process.env.npm_lifecycle_event;
   
  if (TARGET === 'test') {
   console.log(`Running the test task!`);
  }
   
  if (TARGET === 'pretest') {
   console.log(`Running the pretest task!`);
  }
   
  if (TARGET === 'posttest') {
   console.log(`Running the posttest task!`);
  }
  ```
  
  
  
  

### windows下拿到package.json的变量

需要安装cross-env

```js
npm install cross-env --save-dev
```

Package.json:

```js
"page": "test",
  "scripts": {
    "dev": "cross-env PAGE=$npm_package_page cross-env NODE_ENV=dev 
  },
```

在执行 `npm run dev`的时候通过 `process.env.NODE_ENV` 即可获取环境变量 `dev`，通过`process.env.PAGE`即可获取变量`test`





