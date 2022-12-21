# Vue



## 1、Vue的生命周期


![image-20210926145843637](C:\Users\zhuji\AppData\Roaming\Typora\typora-user-images\image-20210926145843637.png)

## 2、Vue 的数据双向绑定是如何实现的

#### Vue2

> 数据劫持
>
> 发布者-订阅者模式

通过 Object.defineProperty 劫持对象的访问器

#### Vue3
> 基于Proxy

与Object.defineProperty(obj, prop, desc)方式相比有以下优势：

1. 丢掉麻烦的备份数据
2. 省去for in 循环
3. 可以监听数组变化
4. 代码更简化

## 3、**computed 和 methods的区别?**

1、computed 是基于响应性依赖来进行缓存的。只有在响应式依赖发生改变时它们才会重新求值, 也就是说, 当msg属性值没有发生改变时, 多次访问 reversedMsg 计算属性会立即返回之前缓存的计算结果, 而不会再次执行computed中的函数。但是methods方法中是每次调用, 都会执行函数的, methods它不是响应式的。

2、computed中的成员可以只定义一个函数作为只读属性, 也可以定义成 get/set变成可读写属性, 但是methods中的成员没有这样的。

## 4、nextTick

在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

- Vue中DOM的更新是异步的。
- 虽然Vue一直强调的是数据驱动视图，但是，有时候不可避免要操作DOM的。
- 在数据变化之后等待 Vue 完成更新 DOM这是一个异步执行的过程，需要用nextTick来搞定。

### nextTick使用情景

- 在Vue生命周期的created()钩子函数进行的DOM操作的时候需要要放在Vue.nextTick()的回调函数中。

- 为啥呢？如果不熟悉created()钩子的话可以再翻看一下Vue的生命周期，created函数执行的时候DOM元素还没有进行过渲染，这个时候操作DOM毛用也没有，所以需要将DOM操作放在Vue.nextTick()的回调方法中去搞定。


- 当然你可以选择在mounted()钩子里去操作DOM，这个时候所有的DOM都挂载到跟元素上并渲染完毕了。这个时候操作DOM元素是没有任何问题的。


数据变化后要执行的某个操作，而这个操作需要使用随数据改变而改变的DOM结构的时候，这个操作都应该放进Vue.nextTick()的回调函数中。

