modjs-autoload-demo
=====================================

modjs-autoload-demo基于[modjs-todo-demo](https://github.com/fouber/modjs-todo-demo) 调整而来，与modjs-todo-demo不同之处在于重点演示如何利用[FIS](https://github.com/fex-team/fis)自动管理同步、异步资源以及自动合并功能。

## modjs-autoload-demo插件介绍
 - 使用[fis-postprocessor-require-async](https://github.com/xiangshouding/fis-postprocessor-require-async) 分析require.async异步加载资源(可选)
 - 使用[fis-postpackager-autoload](https://github.com/hefangshi/fis-postpackager-autoload) 自动加载同步异步资源
 - 使用[fis-postpackager-reqmin](https://github.com/hefangshi/fis-postpackager-reqmin) 按页面自动合并静态资源，提高性能

## 安装fis

```bash
npm install -g fis
```

安装成功后执行 ``fis -h`` 即可看到相关开发命令帮助

## 安装fis的扩展插件，这个项目用到的

```bash
npm install -g fis-postprocessor-require-async #可选
npm install -g fis-postpackager-autoload
npm install -g fis-postpackager-reqmin
```

## 让代码跑起来！

首先，启动内置的调试服务器：

```bash
fis server start
```

此时fis会启动一个精巧的jetty服务器，并且打开浏览器访问了 http://127.0.0.1:8080 ，现在这个调试环境什么也没有，接下来，我们在命令行下cd到我们下载的样例项目中：

```bash
cd modjs-autoload-demo
```

第三步，执行fis的编译命令：

```bash
fis release
```

第四步，刷新浏览器，查看我们的项目。

可以看到所有的模块化资源均已以标签的形式加载进来了

第五步，优化性能

虽然目前项目可以正常运行，但是这种加载方式会造成连接数过多，让我们调整配置，使用[reqmin](https://github.com/hefangshi/fis-postpackager-reqmin)插件为我们合并资源

```bash
fis release -pf fis-conf-with-reqmin.js
```

再次浏览页面，我们可以发现原有的大量静态资源已经自动合并。

## 目录规范

任何 ``目录规范``、``部署规范``、``编译规范`` 都是可配置的（[配置代码](https://github.com/hefangshi/modjs-autoload-demo/blob/master/fis-conf.js)），这里只介绍内置的规范。

内置的规范包括：

1. 扔在 ``modules`` 目录下的js、css、less、coffee文件都是模块化文件，会自动包装define，自己就不要写了。使用require.async或者require加载模块的时候id与文件的对应规则是这样的：
<table>
    <tr>
        <td>文件</td>
        <td>引用id</td>
        <td>举个例子</td>
    </tr>
    <tr>
        <td>/modules/a.js</td>
        <td>a</td>
        <td>require.async('a');</td>
    </tr>
    <tr>
        <td>/modules/b/b.js</td>
        <td>b</td>
        <td>require.async('b');</td>
    </tr>
    <tr>
        <td>/modules/b/c.js</td>
        <td>b/c</td>
        <td>require.async('b/c');</td>
    </tr>
</table>
1. 扔在 ``lib`` 目录下的文件不被认为是模块化的，请直接在页面上使用script或link标签引用。