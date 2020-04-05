`Chen修远` `公瑾` `{docsify-updated} 更新`

*众所周知，前端开发离不开 `NPM`，而 `NPM` 同样离不开 `package.json`，但是日常开发中，大多数人对于 `package.json` 中的大部分属性的用处并不清楚，只知道那几个常用的属性的作用。但，那些出镜率比较低的属性往往在需要它的时候，承担着非常重要的作用。本文针对 `package.json` 的属性进行详细解读，当然重点是那些被忽视的明星们。*

## package.json 如何生成？

当下主流的前端包管理工具有：`npm`、`yarn`、`bower`，本文以 `npm` / `yarn` 为主，生成 `package.json` 的方法如下：

```bash
# npm 创建 package.json
npm init

# yarn 创建 package.json
yarn init
``` 

## name

`name` 为包名称，`string` 类型，一般属于 `package.json` 的必要字段，对于软件包发布十分重要。  
`name` 和 `version` 共同构成一个标识符，该标识符被假定为完全唯一。如果不需要发布软件包，`name` 可以不填。

规则：

> 正则：`^(?:@[a-z0-9-~][a-z0-9-._~]*/)?[a-z0-9-._~]*$`
> 1. 名称必须小于等于 `214` 个字符（包括作用域的包 scope）；  
> 2. 名称不能以 `.` 或 `_` 开头；  
> 3. 新软件包名称中不能包含大写字母；  
> 4. 该名称最终成为 `url`、命令行参数、文件夹名称的一部分，因此，名称不能包含任何非 `url` 安全的字符。

建议：

> 1. 不要使用与核心 `Node` 模块相同的名称；
> 2. 不要在名称中添加 `js` 或 `node`。假定它是 `js`，因为你正在编写 `package.json` 文件，并且可以使用 `engines` 字段指定引擎（见下文）；
> 3. 该名称可能会作为参数传递给 `require()`，因此它应该简短一些，但也应具有描述性；
> 4. 你可能要检查 `npm` 注册表，以查看是否已经具有该名称的软件包，然后再附加它，参阅 [npm 列表](https://www.npmjs.com/)；

名称可以可选地以范围为前缀，例如：`@myorg/mypackage`，参阅 [npm-scope](https://docs.npmjs.com/misc/scope)。

## version

`version` 为包的版本号，`string` 类型，与 `name` 类似，一般属于 `package.json` 的必要字段，对于软件包发布十分重要。  
`name` 和 `version` 共同构成一个标识符，该标识符被假定为完全唯一。如果不需要发布软件包，`version` 可以不填。  

规则：
> 1. 对软件包的更改应与 `version` 的更改同时进行；
> 2. 版本必须可由 [node-semver](https://github.com/npm/node-semver) 解析，它与 `npm` 捆绑在一起作为依赖项（可 npm install semver 安装使用）；

更多版本相关信息，请参阅 [semver](https://docs.npmjs.com/misc/scope)。

## description

`description` 为软件包的描述，`string` 类型，必要字段，有助于使用者发现你的包 `npm search`。

## keywords

`keywords` 为软件包关键字，`string[]` 类型，非必要字段，有助于使用者发现你的包 `npm search`。

## homepage

`homepage` 为项目首页网址，`string` 类型，非必要字段。

## bugs

`bugs` 为项目的问题跟踪器的 `url`、`/` 或报告问题的电子邮件地址，非必要字段，例如：

```json
{
  "url": "https://github.com/owner/project/issues",
  "email": "project@hostname.com"
}
```

## license

`license` 为你为软件包指定的许可证，必要字段，以便使用者知道如何允许他们使用，以及你对该软件包添加的任何限制。

如果你使用的是通用许可证，例如 `BSD-2-Clause` 或 `MIT`，请为你使用的许可证添加当前 `SPDX` 许可证标识符，如下所示：

```json
{
  "license": "BSD-3-Clause"
}
```

你可以查看 `SPDX` 许可证 `ID` 的完整列表。理想情况下，你应该选择经过 `OSI` 批准的产品。如果你的软件包已获得多个通用许可证的许可，请使用 `SPDX` 许可证表达式语法版本 `2.0` 字符串，如下所示：

```json
{
  "license": "(ISC OR GPL-3.0)"
}
```

如果使用的许可证尚未分配 `SPDX` 标识符，或者使用的是自定义许可证，请使用如下所示的字符串值，然后在包的顶层添加一个名为 `<filename>` 的文件：

```json
{
  "license": "SEE LICENSE IN <filename>"
}
```

一些旧软件包使用许可证对象或包含许可证对象数组的 `licenses` 属性：

```json
{
  "license":{
    "type": "ISC",
    "url": "https://opensource.org/licenses/ISC"
  }
}
```

```json
{
  "licenses":[
    {
      "type": "MIT",
      "url": "https://www.opensource.org/licenses/mit-license.php"
    },
    {
      "type": "Apache-2.0",
      "url": "https://opensource.org/licenses/apache2.0.php"
    }
  ]
}
```

以上的形式现在已弃用，而是使用 `SPDX` 表达式，如下所示：

```json
{
  "license": "ISC"
}
```

```json
{
  "license": "(MIT OR Apache-2.0)"
}
```

最后，如果你不希望以任何条款授予他人使用私有或未发布软件包的权利：

```json
{
  "license": "UNLICENSED"
}
```

或者设置 `"private": true` 以防止意外发布。

## people fields: author, contributors

`author` 是一个人，`contributors` 是一群人（数组），一个人用一个对象描述，非必要字段，例如：

```json
{
  "name": "Barney Rubble",
  "email": "b@rubble.com",
  "url": "http://barnyrubble.tumblr.com/"
}
```

或者，你可以将所有内容缩短为一个字符串，然后 `npm` 会为你解析：

```text
"Barney Rubble <b@rubble.com> (http://barnyrubble.tumblr.com/)"
```

电子邮件和网址都是可选的。`npm` 还会为你的 `npm` 用户信息设置一个顶级 `maintainers` 字段。

## files

`files` 为软件包作为依赖项安装时要包括的条目，`string[]` 类型，非必要字段。省略时默认 `["*"]`，表示包含所有文件。

文件格式遵循与 `.gitignore` 相似的语法，但是相反，包含的文件、目录、`*` 等在文件打包的时候包含在其中。某些特殊文件和目录也被包括或排除在外，无论它们是否存在于 `files` 数组中（见下文）。

还可以在包的根目录或子目录中提供 `.npmignore` 文件，以防止文件被包含。规则如下：
> 1. 在程序包的根目录下，它不会覆盖 `files` 字段，但在子目录中，它会覆盖；
> 2. `.npmignore` 文件的工作方式类似于 `.gitignore`；
> 3. 如果存在 `.gitignore` 文件但缺少 `.npmignore` 文件，将使用 `.gitignore` 中的内容；
> 4. 无法通过 `.npmignore` 或 `.gitignore` 排除 `package.json#files` 字段中包含的文件（子目录中，`.npmignore` 会覆盖 `files`）。

无论设置如何，总是会包含下列文件：

- `package.json`
- `README`
- `CHANGES` / `CHANGELOG` / `HISTORY`
- `LICENSE` / `LICENCE`
- `NOTICE`
- `main` 字段中的文件

相反，下列文件总是被忽略：

- `.git`
- `CVS`
- `.svn`
- `.hg`
- `.lock-wscript`
- `.wafpickle-N`
- `.*.swp`
- `.DS_Store`
- `._*`
- `npm-debug.log`
- `.npmrc`
- `node_modules`
- `config.gypi`
- `*.orig`
- `package-lock.json`（改用 `shrinkwrap`）

## main

`main` 为模块的主入口，必要字段，浏览器和 `Node` 端都可以使用。一般 `CommonJS` 规范的包都以 `main` 为主入口，`rollup` 提出对于 `ES` 模块使用 `module` 作为主入口，并且已成为主流构建工具认同的属性，相信很快会被加入到 `package.json` 属性中。

比如用户使用者通过 `require('foo')` 安装 `foo` 程序包，安装的就是 `main` 指定导出的文件。

## browser

`browser` 为浏览器端指定的模块主入口。

## module

`module` 为 `rollup` 提出针对 `ES` 模块的主入口。早期 `npm` 包都是基于 `CommonJS` 规范的，`require()` 会根据 `main` 去找入口文件。而 `es2015` 出现以后，针对 `JavaScript` 和 `Node` 提供了统一的、静态化的模块化规范，且支持 `tree shaking` 特性，并且提高了打包性能。而 `CommonJS` 规范的包都是以 `main` 为入口文件的，如果都用 `main` 会对开发者造成困扰，因此 `rollup` 提出了 `package.json#module` 属性，并且 `webpack` 从 `2.0` 版本开始也支持了该属性。

`main`、`browser`、`module` 三者如何选择：

> 1. 如果 `npm` 包导出的是 `ES` 规范的包，使用 `module`；
> 2. 如果 `npm` 包只在浏览器端使用，并且严禁在 `Node` 端使用，使用 `browser`；
> 3. 如果 `npm` 包只在 `Node` 端使用，使用 `main`；
> 4. 如果 `npm` 包在浏览器端和 `Node` 端都允许使用，使用 `browser` 和 `main`。

## bin

未完待续...

## 参考

- [package.json](https://docs.npmjs.com/files/package.json)
- [NPM package.json](https://www.axihe.com/api/npm/config/package.html)
