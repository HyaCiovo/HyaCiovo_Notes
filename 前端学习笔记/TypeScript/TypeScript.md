# TypeScript

Typed JavaScript at Any Scale.
添加了类型系统的 JavaScript，适用于任何规模的项目。


>  - TypeScript 是添加了类型系统的 JavaScript，适用于任何规模的项目。
  - TypeScript 是一门静态类型、弱类型的语言。
  - TypeScript 是完全兼容 JavaScript 的，它不会修改 JavaScript 运行时的特性。
  - TypeScript 可以编译为 JavaScript，然后运行在浏览器、Node.js 等任何能运行 JavaScript 的环境中。
  - TypeScript 拥有很多编译选项，类型检查的严格程度由你决定。
  - TypeScript 可以和 JavaScript 共存，这意味着 JavaScript 项目能够渐进式的迁移到 TypeScript。
  - TypeScript 增强了编辑器（IDE）的功能，提供了代码补全、接口提示、跳转到定义、代码重构等能力。
  - TypeScript 拥有活跃的社区，大多数常用的第三方库都提供了类型声明。
  - TypeScript 与标准同步发展，符合最新的 ECMAScript 标准（stage 3）。

### TypeScript 是静态类型

类型系统按照**「类型检查的时机」**来分类，可以分为动态类型和静态类型

TypeScript 在运行前需要先编译为 JavaScript，而在  ***编译***  阶段就会进行类型检查

### TypeScript 是弱类型

类型系统按照**「是否允许隐式类型转换」**来分类，可以分为强类型和弱类型

以下这段代码不管是在 JavaScript 中还是在 TypeScript 中都是可以正常运行的，运行时数字 `1` 会被隐式类型转换为字符串 `'1'`，加号 `+` 被识别为字符串拼接，所以打印出结果是字符串 `'11'`。

```js
console.log(1 + '1');
// 打印出字符串 '11'
```

TypeScript 是完全兼容 JavaScript 的，它不会修改 JavaScript 运行时的特性，所以**它们都是弱类型**。



- 联合类型可以被断言为其中一个类型

- 父类可以被断言为子类

- 任何类型都可以被断言为 any

- any 可以被断言为任何类型

- 要使得 `A` 能够被断言为 `B`，只需要 `A` 兼容 `B` 或 `B` 兼容 `A` 即可

  

  任何类型都可以被断言为 any

  any 可以被断言为任何类型
  
  