`Chen修远` `公瑾` `{docsify-updated} 更新`

*在 `ES6` 之前，社区制定了一些模块加载方案，最主要的有 `CommonJS` 和 `AMD` 两种。前者用于服务器，后者用于浏览器。`ES6` 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 `CommonJS` 和 `AMD` 规范，成为浏览器和服务器通用的模块解决方案。参考[阮一峰 ECMAScript 6 入门 - Module 语法](http://es6.ruanyifeng.com/#docs/module)。*

## 静态化的设计思想

`ES6` 设计思想尽量贴近静态化，编译时就能确定模块依赖关系、输入输出变量。`CommonJS`、`AMD` 都只能运行时确定，无法在编译时做静态优化，比如，`CommonJS` 模块就是对象，输入时必须查找对象属性，如下：

```js
// CommonJS 模块
let { stat, exists, readFile } = require('fs');

// 等同于

let _fs = require('fs');
let stat = _fs.stat;
let exists = _fs.exists;
let readfile = _fs.readfile;
```

`ES6` 模块不是对象，模块功能主要由两个命令构成：`export` 和 `import`。`export` 命令用于规定模块的对外接口，`import` 命令用于输入其他模块提供的功能。

```js
// ES6模块
import { stat, exists, readFile } from 'fs';
```

> 上面代码的实质是从 `fs` 模块加载 3 个方法，其他方法不加载。这种加载称为“编译时加载”或者静态加载，即 `ES6` 可以在编译时就完成模块加载，效率要比 `CommonJS` 模块的加载方式高。  
> 编译时加载使得静态分析成为可能，可以进一步扩宽 JavaScript 语法，比如引入宏（`macro`）类型检查（`type system`）这些只能靠静态分析实现的功能。

## 自动采取严格模式

`ES6` 的模块自动采用严格模式，不管你有没有在模块头部加上 `"use strict";`。

## export 命令

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用 `export` 关键字输出该变量。如下：

```js
export const x = 'x';
export let y = 26;
export function fn () { /* ... */ };
export class Class { /* ... */ };

// 等同于

const x = 'x';
let y = 26;
function fn () { /* ... */ };
class Class { /* ... */ };
export {
    x,
    y,
    fn,
    Class,
};
```

`export` 输出的变量为本身的名字，但可以使用 `as` 关键字重命名。如下：

```js
const x = 'x';
// 同一个变量可以被重命名多次
export {
    x as x1,
    x as x2,
    // ...
    x as xx,
};
```

**需要特别注意的是，`export` 命令规定的是对外的接口，必须与模块内部的变量建立一一对应关系。** 如下：

```js
// 报错
export 1;

// 报错
const x = 1;
export x;
```

> 上面两种写法都会报错，因为没有提供对外的接口。第一种写法直接输出 1，第二种写法通过变量 `x`，还是直接输出 1，1只是一个值，不是接口。

```js
// 写法一
export const x = 1;

// 写法二
const x = 1;
export { x };

// 写法三
const y = 1;
export { y as x };
```

> 上面三种写法都是正确的，规定了对外的接口 `x`。其他脚本可以通过这个接口，取到值 1。它们的实质是，在接口名与模块内部变量之间，建立了一一对应的关系。

**另外，`export` 语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值。** 如下：

```js
export let x = 'x';
setTimeout(() => x = 'xx', 500);
```

> 上面代码输出变量 `x`，值为 `'x'`，500 毫秒后变为 `'xx'`。  
> 这一点与 `CommonJS` 规范完全不同。`CommonJS` 模块输出的是值的缓存，不存在动态更新。

**最后，`export` 命令可以出现在模块的任何位置，只要处于模块顶层就可以。如果处于块级作用域内，就会报错，`import` 命令也是如此。这是因为处于条件代码块之中，就没法做静态优化了，违背了 `ES6` 模块的设计初衷。**

**总结：**
1. 两种输出方式
2. 输出变量名可用 `as` 关键字重命名
3. 对外输必须与模块内部变量建立一一对应关系
4. 对外输出的接口与模块内变量值为动态绑定关系
5. `export` 可以出现在模块的任何位置，但必须处于模块顶层，不能包含在块级作用域内

## export default 命令

从上面 `export` 命令可知道，用户使用 `import` 加载 `export` 输出的时候需要知道输出变量的名字，否则无法加载。为了提供方便，`ES6` 提供了 `export default` 命令，为模块指定默认输出。如下：

```js
// x.js
export default 'x';

// 或者

const x = 'x';
export default x;
```

其他模块加载该模块时，`import` 命令可为该匿名变量指定任意名字。如下：

```js
import xxx from './x';
```

`export default` 命令用在非匿名函数前，也是可以的。如下：

```js
export default function fn () { /* ... */ };

// 或者

function fn () { /* ... */ };
export default fn;
```

> 上面代码中，`fn` 函数的函数名 `fn`，在模块外部是无效的。加载的时候，视同匿名函数加载。

默认输出和正常输出在使用 `import` 的时候应注意，如下：

```js
// x.js
export default 'x';
// y.js
import x from './x';

// x.js
export const x = 'x';
// y.js
import { x } from './x';
```

`export default` 命令用于指定模块的默认输出。一个模块只能有一个默认输出，因此 `export default` 命令只能在一个模块中使用一次。

本质上，`export default` 就是输出一个叫做 `default` 的变量或方法，然后系统允许你为它取任意名字。所以，下面的写法是有效的，如下：

```js
// x.js
const x = 'x';
export { x as default };
// 等同于
export default x;

// y.js
import { default as x } from './x';
//等同于
import x from './x';
```

正是因为 `export default` 命令其实只是输出一个叫做 `default` 的变量，所以它后面不能跟变量声明语句。如下：

```js
// 正确
export const x = 'x';

// 正确
const x = 'x';
export default x;

// 错误
export default const x = 'x';
```

> 上面代码中，`export default x` 的含义是将变量 `x` 的值赋给变量 `default`。所以，最后一种写法会报错。

同样地，因为 `export default` 命令的本质是将后面的值，赋给 `default` 变量，所以可以直接将一个值写在 `export default` 之后。如下：

```js
// 正确
export default 1;

// 报错
export 1;
```

> 上面代码中，后一句报错是因为没有指定对外的接口，而前一句指定对外接口为 `default`。

有了 `export default` 命令，输入模块时就非常直观了，以输入 `lodash` 模块为例，如下：

```js
import _ from 'lodash';
```

如果想在一条 `import` 语句中，同时输入默认方法和其他接口，可以写成下面这样。如下：

```js
import _, { each, forEach } from 'lodash';

// 对应上面代码的输出如下：
export default function (obj) { /* ... */ };
export function each(obj, iterator, context) { /* ... */ };
export { each as forEach };
```

**总结：**

1. `export default` 为默认输出，不需要名、值对应，因此可将一个值、或一个变量直接输出
2. `export default` 输出的为匿名变量，外部输入时可以自定义名字
3. 一个模块只能有一个默认输出
4. `export default` 后不能输出变量声明语句
5. `export default` 命令实际上是将值赋给 `default` 变量，相当于 `export` 输出一个 `default` 变量的声明语句

## import 命令

上面 `export` 和 `export default` 已经介绍了大部分 `import` 的用法，此处列一下其基本使用，如下：

```js
// 输入 index.js 中 export 输出的内容
// 1. 单独引入
import { x, y, z } from './index';
// 2. 全部引入到一个对象
import * as index from './index';
// 3. 可重命名
import { x as xx, y as yy, z } from './index';

// 输入 index.js 中 export default 输出的内容
import index from './index';
//或者
import { default as index } from './index';
```

`import` 命令输入的变量都是只读的，因为它的本质是输入接口，不允许在加载模块的脚本里面，改写接口。如下：

```js
import { x } from './index'
x = {}; // Syntax Error : 'x' is read-only;
```

> 上面代码中，脚本加载了变量 `x`，对其重新赋值就会报错，因为 `x` 是一个只读的接口。

如果 `x` 是一个对象，改写 `x` 的属性是允许的。

```js
import { x } from './index'
x.a = 'a'; //合法
```

> 上面代码中，`a` 的属性可以成功改写，并且其他模块也可以读到改写后的值。不过，这种写法很难查错，建议凡是输入的变量，都当作完全只读，轻易不要改变它的属性。

**注意，`import` 命令具有提升效果，会提升到整个模块的头部，首先执行。** 如下：

```js
fn();
import { fn } from './index';
```

> 上面的代码不会报错，因为 `import` 的执行早于 `fn` 的调用。这种行为的本质是，`import` 命令是编译阶段执行的，在代码运行之前。

由于 `import` 是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。

```js
// 报错
import { 'f' + 'n' } from './index';

// 报错
let index = './index';
import { fn } from index;

// 报错
if (x === 1) {
  import { fn } from './index1';
} else {
  import { fn } from './index2';
}
```

**`import` 语句会执行所加载的模块。** 如下：

```js
import 'lodash';
```

> 上面代码仅仅执行 `lodash` 模块，但是不输入任何值。

如果多次重复执行同一句 `import` 语句，那么只会执行一次，而不会执行多次。如果多次重复执行同一句 `import` 语句，那么只会执行一次，而不会执行多次。

```js
import 'lodash';
import 'lodash';
// 上面 lodash 加载了两次，但只执行一次

import { x } from './index';
import { y } from './index';
// 等同于
import { x, y } from './index';
```

模块整体加载所在的那个对象，应该是可以静态分析的，所以不允许运行时改变。下面的写法都是不允许的。如下：

```js
import * as index from './index';

// 下面两行都是不允许的
index.x = 'x';
index.fn = function () {};
```

## export 与 import 的复合写法

在一个模块之中，先输入后输出同一个模块，`import` 语句可以与 `export` 语句写在一起。

```js
export { x, y } from './index';

// 可以简单理解为
import { x, y } from './index';
export { x, x };
```

> 上面代码中，`export` 和 `import` 语句可以结合在一起，写成一行。但需要注意的是，写成一行以后，`x` 和 `y` 实际上并没有被导入当前模块，只是相当于对外转发了这两个接口，导致当前模块不能直接使用 `x`和 `y`。

一下列出常用用法，如下：

```js
// 接口改名
export { x as xx } from './index';

// 整体输出
export * from './index';

// 默认接口输出
export { default } from './index';

// 具名接口改为默认接口
export { index as default } from './index';

// 默认接口改为具名接口
export { default as index } from './index';
```

**总结：**

1. 符合写法实际上并没有被导入当前模块，只是相当于对外转发了这两个接口

## import()

前面介绍过 `import` 为编译时加载，无法在运行时加载，因此不能动态加载资源，`require` 的动态加载，如下：

```js
const path = './' + fileName;
const myModual = require(path);
```

因此 `ES6` 有提案加入 `import()` 函数动态异步加载资源，返回 `Promise` 对象。

> - `import()` 函数可以用在任何地方，不仅仅是模块，非模块的脚本也可以使用。它是运行时执行，也就是说，什么时候运行到这一句，就会加载指定的模块。
> - `import()` 函数与所加载的模块没有静态连接关系，这点也是与 `import` 语句不相同。
> - `import()` 类似于 `Node` 的 `require` 方法，区别主要是前者是异步加载，后者是同步加载。

**总结：**

1. 动态加载
2. 异步加载
3. 返回 `Promise` 对象

## 未来可期

- 不再需要 `UMD` 模块格式了，将来服务器和浏览器都会支持 `ES6` 模块格式。目前，通过各种工具库，其实已经做到了这一点
- 将来浏览器的新 API 就能用模块格式提供，不再必须做成全局变量或者 `navigator` 对象的属性
- 不再需要对象作为命名空间（比如 `Math` 对象），未来这些功能可以通过模块提供

## 参考

- [阮一峰 ECMAScript 6 入门 - Module 语法](http://es6.ruanyifeng.com/#docs/module)
- [What is AMD, CommonJS, and UMD?](https://www.davidbcalhoun.com/2014/what-is-amd-commonjs-and-umd/)  