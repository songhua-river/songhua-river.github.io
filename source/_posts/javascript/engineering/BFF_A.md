---
title: BFF 实践 (1)
mathjax: true
categories:
  - JavaScript
  - 工程化
tags:
  - BFF
  - 工程化
abbrlink: 25f7c524
date: 2020-12-10 14:56:09
---


#### BFF

BFF，即 Backend For Frontend（服务于前端的后端），也就是服务器设计 API 时会考虑前端的使用，并在服务端直接进行业务逻辑的处理，又称为用户体验适配器。BFF 只是一种逻辑分层，而非一种技术，虽然 BFF 是一个新名词，但它的理念由来已久。

![](0001.webp)

通常一个页面的请求包含了多个不同的请求，用于页面组件的渲染

同时为了保障 Android，iOS，以及 Web 端的不同需求，需要为不同的平台写不同的 API 接口，而每当值发生一些变化时，需要 Android，iOS，Web 做出修改。

![](0002.webp)

有了 BFF 这一层时，我们就不需要考虑系统后端的迁移。后端发生的变化都可以在 BFF 层做一些响应的修改

![](0003.webp)


##### BFF 场景


**多端应用**

我们在设计 API 时会考虑到不同设备的需求，也就是为不同的设备提供不同的 API，虽然它们可能是实现相同的功能，但因为不同设备的特殊性，它们对服务端的 API 访问也各有其特点，需要区别处理。

**服务聚合**

随着微服务的兴起，原本在同一个进程内运行的业务流程被拆分到了不同的服务中。这在增加业务灵活性的同时，也让前端的调用变得更复杂。BFF 的出现为前端应用提供了一个对业务服务调用的聚合点，它屏蔽了复杂的服务调用链，让前端可以聚焦在所需要的数据上，而不用关注底层提供这些数据的服务。


##### 实战中的玩法

**访问控制**

例如，服务中的权限控制，将所有服务中的权限控制集中在 BFF 层，使下层服务更加纯粹和独立。

**应用缓存**

项目中时常存在一些需要缓存的临时数据，此时 BFF 作为业务的汇聚点，距离用户请求最近，遂将该缓存操作放在 BFF 层。

**第三方入口**

在业务中需要与第三交互时，将该交互放在 BFF 层，这样可以只暴露必要信息给第三方，从而便于控制第三方的访问。


#### 初始化项目

##### 项目目录结构划分

![](0001.png)

##### package.json 生命周期 并行执行

安装webpack

```bash
yarn add -D webpack-cli webpack
```

package.json 文件添加

```json
  "scripts": {
    "test": "echo test",
    "pretest": "echo pretest"
  },
```

执行 `yarn test`

```bash
yarn run v1.22.10
$ echo pretest
pretest
$ echo test
test
Done in 0.05s.
```

并行执行，不能保证顺序

```json
"scripts": {
  "test1": "echo test1",
  "test2": "echo test2",
  "test": "yarn test1 & yarn test2"
}
```

```bash
yarn test
yarn run v1.22.10
$ yarn test1 & yarn test2
$ echo test2
$ echo test1
test2
test1
Done in 0.25s.
```


##### scripy

使用scripty拆分复杂命令

```bash
yarn add -D scripty
```

package.json

```json
"scripts": {
  "test:one": "scripty",
  "test:two": "scripty",
  "test": "scripty"
}
```

按照命令建立文件夹

![](0002.png)

执行 `yarn test` test文件夹下面的所有命令

##### package.json 定义公共参数

package.json

```json
"config":{
  "port":9999
}
```

通过变量在命令配置中使用  

script/test/one.sh

```sh
echo $npm_package_config_port
```

执行 `yarn test` 打印端口

```bash
yarn run v1.22.10
$ scripty
scripty > Executing "/home/supreme/Workspace/mvc/scripts/test/one.sh":

9999
scripty > Executing "/home/supreme/Workspace/mvc/scripts/test/two.sh":

2
Done in 0.13s.
```

##### jscpd 代码重复率检查

```bash
yarn add -D jscpd
```

添加配置文件.jscpd.json

```json
{
  "threshold": 0,
  "reporters": ["html", "console"]
}
```

scripts

```bash
jscpd --min-lines 1 --output ./doc/jscpd --pattern "src/**/*.js"
```

##### webpack配置

建立配置文件夹 config,通过内置方法拿到配置参数

```javascript
process.env.NODE_ENV
```

安装 [yargs](https://github.com/yargs/yargs),以对象的形式更方便的获取命令参数

安装 [webpack-merge](https://github.com/survivejs/webpack-merge) 合并公共配置和定制配置

##### swig

使用swig作为后端模板，用于服务端渲染html