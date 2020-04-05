`Chen修远` `公瑾` `{docsify-updated} 更新`

*`ES6` 之前前端都没有一个官方的、统一的模块加载规范，社区衍生了一些（如：`CommonJS`、`AMD`、`CMD`、`UMD` 等），纵然如此多的模块加载规范，也只是各在各的适用圈子里风生水起，前端模块化仍然四分五裂。`ES6` 的模块规范的出现给这种局面一个很值得憧憬的未来。*

## ES6

`ES` 模块规范详见 [模块化-ES6](https://iamztf.github.io/Xiuyuan.doc/#/code/f2e/module/es6)

## CommonJS

`CommonJS` 是被 `Node` 一直采用的服务器端模块加载规范，也可能是以前、现在都被广泛使用的机制，`Webpack` 也是默认使用 `CommonJS` 机制。
  
`CommonJS` 模块一个文件既是一个 `module`，每个单独的 `module` 都有一个单独的作用域，其内部变量和函数都是私有的，外部不可见。

CommonJS 分为三部分：

> 1. `require` 模块加载
> 2. `exports` 模块导出
> 3. `module` 模块本身

案例如下：

```js
// module.1.js
function func () {}
module.exports = func;
```
```js
// module.2.js
module.exports = {};
```
```js
// module.3.js
var func = require('./module.1.js');
var obj = require('./module.2.js');
```

特点：

> 1. 输出的是一个拷贝值，外部的修改不会对模块造成影响；
> 2. 运行时加载；
> 3. 同步加载，必须加载完成才能执行后续操作（这也是浏览器端为什么会出现 `AMD` 和 `CMD` 的原因）。

## AMD

*提起 `AMD` 就想起了当年用 `RequireJS` 的日子，`RequireJS` 应该算是我在浏览器端使用的第一个模块化工具了。*

`AMD` Asynchronous Module Definition，异步模块加载规范，`RequireJS` 是 `AMD` 规范的代表性产物。

`AMD` 分为两部分：

```js
// define(id?, dependencies?, factory); 
// require([module], callback);

// 案例
define(['./a','./b'], function (a, b) {
    a.test();
    b.test();
    
    return { a, b };
});

// 案例
define(function (require, exports, module) {
    var a = require('./a');
    var b = require('./b');
    
    return { a, b };
})
```

## CMD

`CMD` Common Module Definition，`SeaJS` 的规范产物。

**案例如下：**

```js
define(function (require, exports, module) {
    //依赖可以就近书写
    var a = require('./a');
    a.test();
    
    //软依赖
    if (status) {
        var b = require('./b');
        b.test();
    }
});
```

## UMD

`UMD` Universal Module Definition，被推出做前后端大一统的模块加载规范，支持 `AMD` 和 `CommonJS` 模式。

步骤如下：

> 1. 先判断是否支持 `CommonJS` 模块格式（`exports` 是否存在），存在则使用 `CommonJS` 格式；
> 2. 再判断是否支持 `AMD`（`define` 是否存在），存在则使用 `AMD` 方式加载模块；
> 3. 前两个都不存在，则将模块公开到全局（`window` 或 `global`）。

案例如下：

```js
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD
        define(['jquery'], factory);
    } else if (typeof exports === 'object') {
        // Node, CommonJS-like
        module.exports = factory(require('jquery'));
    } else {
        // Browser globals (root is window)
        root.returnExports = factory(root.jQuery);
    }
}(this, function ($) {
    //    methods
    function myFunc(){};

    //    exposed public method
    return myFunc;
}));
```

## 参考

- [What is AMD, CommonJS, and UMD?](https://www.davidbcalhoun.com/2014/what-is-amd-commonjs-and-umd/)  
- [require.js cn](http://www.requirejs.cn/docs/api.html), [require.js us](https://requirejs.org/docs/api.html)