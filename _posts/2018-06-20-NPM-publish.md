---
layout: post
title: "NPM包的发布"
date: 2018-06-22 15:03:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- npm
- travis
meta:
  _edit_last: '1'
---

### 1.测试代码

在开发者编写了一个npm包后，最关键的其实就是测试部分，如果只是内部使用的话出了错误改动代码的成本还比较小，如果发布到npm上，几万人在使用的一个包如果出了问题，可能影响的产品就太多了。

在这里推荐使用自动化的测试工具[travis](https://travis-ci.org/),当然工具还有很多种，选择最适用的就好。

Travis是与github绑定的，如果你的代码管理在github上，那么每次github仓库的代码更新，travis都会自动运行一遍测试脚本，如果本次代码更新出了问题，那么travis的build就会失败，并告知失败原因。

测试脚本都是针对功能的，每一个功能至少要有一个测试脚本。

使用travis也很简单，在代码项目里维护一个文件“.travis.yml”。

文件的格式内容有很多，根据开发者需求不同，配置的内容也不同，具体可以参看[.travis.yml](https://docs.travis-ci.com/user/customizing-the-build)

以一个Javascript的npm包为例，

```yaml
language: node_js
node_js:
 - "lts/*"
script:
 - "if [[ -n $LINT_TEST ]]; then npm run lint; fi"
 - npm run test
```

<!--more-->

它表明环境语言是node_js,安装版本为最近发布版本，运行内容为运行package.json的脚本中的“test”内容。

然后就可以编写测试脚本了，Mocha是Javascript的测试框架，既可以运行在浏览器环境也可以运行在node.js上，它的用法可以参看[Mocha官网](https://mochajs.org/)，或者阮一峰的博客[测试框架 Mocha 实例教程](http://www.ruanyifeng.com/blog/2015/12/a-mocha-tutorial-of-examples.html)。

### 2.配置信息

当测试任务完成后，就可以安心地准备npm包发布事情了，首先要在package.json里配置信息。

可以运行`npm init`来自动生成package.json文件，也可以手工生成。

配置项：

| 名称        |  说明     | 
| --------    |  ------- |
| name        | 项目名称  |  
| version     | 项目版本号，版本规则[semver](https://docs.npmjs.com/misc/semver)  |
| description | 项目描述        |
| main        | 指定主入口，默认值为根目录下的index.js        |
| dependencies| 运行环境依赖        |
| devDependencies| 开发环境依赖        |
| scripts     | 指定npm脚本        |
| repository  | 配置代码库        |
| keywords    | 指定关键字，便于开发者搜索        |
| author      | 配置作者信息        |
| license     | 配置许可类型        |
| bugs        | 配置问题反馈链接        |
| homepage    | 配置项目主页        |

还有一些配置项，具体可参看[npm package.json](https://docs.npmjs.com/files/package.json)

### 3.版本发布

最后发布就很简单了，先去[npm官网](https://www.npmjs.com/)注册一个账号。

注册后在本地运行`npm login`进行登陆。

然后在项目根目录运行`npm publish`即可发布。

建议版本号不要手动修改，如果需要更新包版本，先运行`npm version <update_type>`，这个是更新版本的命令，package.json会自动进行更新。

然后再次运行`npm publish`即可更新。

**强烈建议不要轻易运行`npm unpublish`来废除npm包，因为你不知道会有多少项目已经引用了这个包，这会带来不可预知的结果。**

如果想要废除这个包，可以用`npm deprecate`来让使用者看见警告提示，进行更新。具体可见[deprecate
](https://docs.npmjs.com/cli/deprecate)。


