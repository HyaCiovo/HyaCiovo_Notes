# React近期学习

> React 是一个由Facebook公司开源的用于构建 UI 用户界面的 JS 框架。
>
> 核心设计思路是：声明式、组件化和通用性。



## JSX

JSX是一个JavaScript的语法扩展，结构类似XML。

其主要用于声明React元素，使用了JSX后，在打包过程中会通过Babel插件编译为React.createElement，

JSX更像是JavaScript的语法糖，使得其开发更贴近JS原生，不需要引入过多的新概念，也能够获得良好的编辑器代码提示。

其与Vue的template模板比起来更自由，不需要引入额外的模板语法和模板指令。

> Vue使用template模板，引入模板规范，可以获得优秀的静态代码分析。可以获得更好的解析、Diff效率。
>
> Vue当中也可以使用JSX进行开发，但是这样就会失去静态代码分析的支持



## 生命周期

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b5fa490ee4d948dba86a950bfe08dede~tplv-k3u1fbpfcp-zoom-in-crop-mark:4536:0:0:0.awebp" alt="img" style="zoom:50%;" />



> **注：红色为 React 17 已经废弃的生命周期钩子，绿色为新增的生命周期钩子**



1. getDerivedStateFromProps	

   > 作用:	让组件在props变化时更新state
   >
   > 注意事项：	1. props传入时即可调用	2. 反模式：复制props到state；在props变化后修改state

2. **UNSAFE**_componentWillMount    

   > 作用：过去用于网络请求或者绑定函数
   >
   > 注意事项：	**已弃用** 	异步渲染模式下，该方法可能会被多次调用

3. componentDidMount    

   > 作用：发起网络请求或者绑定事件

4. **UNSAFE**_componentWillReceiveProps    

   > 作用：与getDerivedStateFromProps一致
   >
   > 注意事项： **已弃用**

5. shouldComponentUpdate

   > 作用：通过返回true或者false来确定是否需要触发重新渲染
   >
   > 注意事项：1. 可通过添加判断条件来进行性能优化	2. PureComponent的实现原理

6. **UNSAFE**_componentWillUpdate

   > 作用：执行更新操作
   >
   > 注意事项：**已弃用**	异步渲染机制下可能会被多次调用

7. getSnapshotBeforeUpdate

   > 作用：返回一个数据，作为coponentDidUpdate的第三个参数传入
   >
   > 注意事项：1. 可在Dom更新时执行某些运算	2. 不可以使用setState

8. componenDidUpdate

   > 作用：视图更新后进行操作
   >
   > 注意事项：谨慎使用setState，避免死循环

9. componentWillUnmount

   > 作用：执行解除事件绑定，取消定时器等清理工作



## **组件重新渲染机制**

1. 函数组件

   > 任何情况下都会重新渲染，可以使用React.memo优化，跳过渲染

2. React.Component

   > **state**发生变化时会重新渲染；
   >
   > **props**传入时，无论是否发生变化都会重新渲染；
   >
   > **shouldComponentUpdate**决定是否需要重新渲染。

3. React.PureComponent

   > 默认实现 shouldComponentUpdate，**浅比较 **state和props



## 类组件和函数组件的区别

- 对组件而言，类组件和函数组件在使用与呈现上没有任何不同，性能表现上，在现代浏览器中也不会有明显差异。
- 类组件拥有组件实例和完整的生命周期，函数组件不存在生命周期，主要依赖`useEffect`hook来实现。
- 但是在开发者的心智模型上却有着巨大的差异。类组件基于面向对象编程 (OOP)，主打继承。生命周期这些核心概念。而函数组件基于函数式编程 (FP)，主打 immutable、无副作用、引用透明等特点。
- 性能优化上，类组件主要依靠 shouldComponentUpdate 阻断渲染提升渲染性能；而函数组件依靠 React.memo 缓存渲染结果并跳过渲染来提升性能。
- 由于 React Hooks 的推出，函数组件成为了社区未来主推的方案。 类组件在未来时间切片与并发模式中，由于生命周期带来的复杂度，并不易于优化。而函数组件本身清凉简单，且在 Hooks 的基础上提供了比原先更加细粒度的逻辑组织与复用，更能适应 React 未来的发展。



## setState的同步异步

setState 并非真正异步，只是看上去像是一个异步。在源码中，通过 **isBatchingUpdates** 来判断 setState 是先存入 state 队列还是直接更新，如果值为 true 则执行异步操作，为 false 则执行更新。

在 React 可控制的地方，就为 true，比如在 React 生命周期事件和合成事件中，都会走合并操作，延迟更新的策略。

但在 React 无法控制的地方，比如原生事件 (addEventListener / setTimeout / setInterval) 中只能同步更新。

一般认为，异步设计的目的在于性能优化，减少渲染次数，React 还补充了两点：

> 1. 保持内部一致性。如果将 setState 改为同步更新，那么尽管 state 的更新是同步的，但 props 不是。
> 2. 启用并发更新，完成异步渲染。



## Diff算法

diff算法有如下三个策略：

1. DOM节点跨层级的移动操作发生频率很低，是次要矛盾；
2. 拥有相同类的两个组件将会生成相似的树形结构，拥有不同类的两个组件将会生成不同的树形结构，这里也是抓前者放后者的思想；
3. 对于同一层级的一组子节点，通过唯一id进行区分，即没事就warn的key。
   基于各自的前提策略，React也分别进行了算法优化，来保证整体界面构建的性能



























































