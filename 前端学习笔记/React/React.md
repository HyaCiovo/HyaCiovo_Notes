# React学习笔记

- 1 使用 JSX语法 创建组件，实现组件化开发，**为函数式的 UI 编程方式打开了大门**

- 2 性能高的让人称赞：通过 `diff算法` 和 `虚拟DOM` 实现视图的高效更新

- 3 HTML仅仅是个开始，JSX to everything

  

[React入门看这篇就够了 - SegmentFault 思否](https://segmentfault.com/a/1190000012921279)

- #### 组件化开发 ReactNative

- #### 核心概念：虚拟dom、Diff算法

  虚拟dom：使用JavaScript构建dom树结构，然后构建真正的dom树；当状态发生变更时，重新构建新的对象树；将新对象树的差异应用到真正的dom树上。

  Diff算法：render()函数创建一个React元素树，在下一个state或props更新时，创建新的React树，React比较两棵树的不同，计算出如何高效地更新UI（**只更新变化的地方**）

  - React中有两种假定：
    - 1 **两个不同类型的元素会产生不同的树(根元素不同结构树一定不同)**
    - 2 **开发者可以通过key属性指定不同树中没有发生改变的子元素**

- #### Diff算法的说明：

  1. 如果两棵树的根元素类型不同，React会销毁旧树，创建新树

  2. 对于类型相同的React DOM 元素，React会对比两者的属性是否相同，只更新不同的属性；当处理完这个DOM节点，React就会递归处理子节点

  3. 当在子节点的后面添加一个节点，这时候两棵树的转化工作执行的很好，但如果在开始位置插入元素，react将改变每一个子元素删除重新创建。所以引入key属性，当子节点带有key属性，React会通过key元素匹配原始树和后来的树：

     - key属性在React内部使用，但不会传递给你的组件

     - 在遍历数据时，推荐在组件中使用 key 属性：`<li key={item.id}>{item.name}</li>`

     - **key只需要保持与他的兄弟节点唯一即可，不需要全局唯一**

     - **尽可能的减少数组index作为key，数组中插入元素等操作时，会使得效率底下**

       > 理解：当以数组的下标index作为key值时，其中一个元素发生了变化 就有可能导致所有元素的key值发生改变  diff算法是比较同级之间的不同  以key来进行关联 当对数组进行下标的变换时，比如删除第一条数据，那么以后所有的Index都会发生改变，那么key自然也跟着全部发生改变， 所以index作为key值是不稳定的，这种不稳定性有可能导致性能的浪费 导致diff无法关联起上一次一样的数据  因此 能不用Index作为key就不要用Index 
  
  

- #### 组件的生命周期

  - 组件的生命周期包含三个阶段：创建阶段（Mounting）、运行和交互阶段（Updating）、卸载阶段（Unmounting）

  - Mounting：

    > constructor()
    > //1、获取props 2、初始化state
    > componentWillMount()
    > render()
    > componentDidMount()
    
  - Updating：

    > componentWillReceiveProps()
  > shouldComponentUpdate()
    > componentWillUpdate()
    > render()
    > componentDidUpdate()
    
  - Unmounting:
  
    > componentWillUnmount()

  

- #### 组件通讯

  - 父 -> 子：`props`
  - 子 -> 父：父组件通过props传递回调函数给子组件，子组件调用函数将数据作为参数传递给父组件
  - 兄弟组件：因为React是单向数据流，因此需要借助父组件进行传递，通过父组件回调函数改变兄弟组件的props
  - React中的状态管理： flux（提出状态管理的思想） -> Redux -> mobx（移动端）
  - Vue中的状态管理： Vuex
  - 简单来说，就是统一管理了项目中所有的数据，让数据变的可控
  - 组件通讯