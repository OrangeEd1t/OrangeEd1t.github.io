---
title: npm依赖包的安装、更新和删除
date: 2020-07-10 11:43:32
---

# 安装

* 安装但不写入package.json

```
npm install xxx 
```

* 安装并写入package.json的"dependencies"中

``` 
npm install xxx -S
```

* 安装并写入package.json的"devDependencies"中

```
npm install xxx -D
```

* 全局安装

```
npm install xxx -g
```

* 安装指定版本

```
npm install xxx@1.2.0
```

# 更新

首先，我们需要检查过期的依赖包。

```
npm outdated
```

执行该命令，我们可以看到所有已经过期的依赖包，接下来我们安装”npm-check-updates“模块。（若已安装，则跳过该步骤）

```
npm install npm-check-updates -g
```

安装完成后，我们执行以下命令，检查需要更新的模块

```
nuc
```

或者

``` 
npm-check-updates
```

接下来，我们就开始更新package.json的依赖包到最新版本

```
ncu -u
```

>  以上命令执行，会更新全部模块。但在实际开发中不建议一次全部更新，可以根据实际需要，更新指定的模块，并且可以根据作用范围在后面加上 -D、-S 或 -g

最后，为了保险起见，package.json 更新后，可删除整个node_modules目录并执行以下命令来重新初始化项目

```
npm install
```

# 删除

* 删除指定模块

``` 
npm uninstall xxx
```

* 删除全局模块

```
npm uninstall -g xxx
```

