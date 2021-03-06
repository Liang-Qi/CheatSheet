# 速查表

## Node 和 Npm

### 参数传递

package.json

```json
{
  "name": "nev",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "node app.js",
    "test-argv": "node app.js --arg=123",
    "test-npm-config": "npm run test --arg=123",
    "test-npm-package-config": "npm run test --arg=123",
    "set-npm-package-config": "npm config set nev:arg 123"
  },
  "author": "",
  "license": "ISC"
}
```

app.js

```javascript
console.log(process.argv) // 一个数组 ['node 的 路径', 'app.js 的 路径', ......]
console.log(process.env.npm_config_arg)
console.log(process.env.npm_package_config_arg)
// npm_package_config_arg 的值取决于 .npmrc 文件中的内容。
// 使用 npm config list 查看。
```

### node中的global与浏览器中的window

在浏览器中，顶层作用域是全局作用域。 这意味着在浏览器中 var something 将定义一个新的全局变量。 在 Node.js 中，这是不同的。 顶层作用域不是全局作用域，Node.js 模块中的 var something 的作用域只在该模块内。

程序运行在非“严格模式”下。当引擎执行 LHS 查询时，如果在顶层（全局作用域）中也无法找到目标变量，
全局作用域中就会创建一个具有该名称的变量，并将其返还给引擎。

```javascript
\* Node环境 *\

a = 1 \\ 执行LHS查询，无法找到目标变量 a，所以在 global 上创建了 a

var b = 2 

function c() {

}

\\ b 和 c 都创建在node模块内的作用域

console.log(global.a)  // 1
console.log(global.b)  // undefined
console.log(global.c)  // undefined
```

```javascript
\* 浏览器环境 *\
a = 1

var b = 2

function c() {}

console.log(window.a)  // 1
console.log(window.b)  // 2
console.log(window.c)  // function c() {}
```

在《你不知道的JavaScript （上卷）》中有以下代码及描述

```javascript
function foo() {
  var a = 2;
  this.bar();
}

function bar() {
  console.log( this.a );
}

foo();
```

> 这段代码中的错误不止一个。虽然这段代码看起来好像是我们故意写出来的例子，但是实
> 际上它出自一个公共社区中互助论坛的精华代码。这段代码非常完美（同时也令人伤感）
> 地展示了 this 多么容易误导人。 
> 
> 首先，这段代码试图通过 this.bar() 来引用 bar() 函数。$这是绝对不可能成功的，我们之
> 后会解释原因。$调用 bar() 最自然的方法是省略前面的 this，直接使用词法引用标识符。
> 此外，编写这段代码的开发者还试图使用 this 联通 foo() 和 bar() 的词法作用域，从而让
> bar() 可以访问 foo() 作用域里的变量 a。这是不可能实现的，你不能使用 this 来引用一
> 个词法作用域内部的东西。  
> 
> 每当你想要把 this 和词法作用域的查找混合使用时，一定要提醒自己，这是无法实现的。

这段代码在浏览器环境可以正常执行并且输出一个 undefined，而在node环境下会获得 TypeError: this.bar is not a function 。

```javascript
(function () {
  function foo() {
    var a = 2;
    this.bar();
  }

  function bar() {
    console.log( this.a );
  }

  foo();
})()
```

这段代码在浏览器环境和node环境，都会获得 TypeError: this.bar is not a function 。~~~~

## JavaScript
### new 运算符
> new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。JavaScript new 运算符

## 语法

> new constructor[([arguments])]

参数 | 含义
---|---
constructor | 一个指定对象实例的类型的类或函数。
arguments | r一个用来被constructor 调用的参数列表。

## 描述

创建一个用户自定义的对象需要两步：

通过编写函数来定义对象类型。
- 通过new来创建对象实例。
- 创建一个对象类型，需要创建一个指定其名称和属性的函数；对象的属性可以指向其他对象，看下面的例子：

当代码 new Foo(...) 执行时，会发生以下事情：

- 一个继承自 Foo.prototype 的新对象被创建。
- 使用指定的参数调用构造函数 Foo ，并将 this 绑定到新创建的对象。new Foo 等同于 new Foo()，也就是没有指定参数列表，Foo 不带任何参数调用的情况。
- 由构造函数返回的对象就是 new 表达式的结果。如果构造函数没有显式返回一个对象，则使用步骤1创建的对象。（一般情况下，构造函数不返回值，但是用户可以选择主动返回对象，来覆盖正常的对象创建步骤）


```javascript
function newObj (Constructor) {
  var args = []

  for (let index = 1; index < arguments.length; index++) {
    args.push(arguments[index])
  }

  // 一个继承自 Constructor.prototype 的新对象被创建
  var self = Object.create(Constructor.prototype)

  // 使用指定的参数调用构造函数 Constructor ，并将 this 绑定到新创建的对象。
  var obj = Constructor.apply(self, args)

  // 由构造函数返回的对象就是 newObj 的返回值。
  if (
    typeof obj === 'object' &&
    obj !== undefined &&
    obj !== null
  ) {
    return obj
  }

  // 如果构造函数没有显式返回一个对象，则使用对象self。
  return self
}

function Car (name) {
  this.name = name
}

var car = newObj(Car, '福特')

console.log(car)
console.log(car instanceof Car)
```

一种防御式编程

```javascript
function Car (name) {
  if (!this instanceof Car) {
    throw new Error('should be called with the new !')
  }
  if (new.target !== Car) { // es6
    throw new Error('should be called with the new !')
  }
  this.name = name
}
```

## git
### VS CODE 提交密码
```
git config --global credential.helper store
```