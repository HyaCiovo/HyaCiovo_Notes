# 前言

该笔记是本人基于有了一定的Vue2、React使用经验，记录的自己认为比较重要的和有特色的部分。这并不代表没有记录的部分就是不重要的，详细了解需要到官方文档查看，尤其是英文文档。

![查看源图像](https://tse1-mm.cn.bing.net/th/id/OIP-C.2THtavcQEe_PIVs8A8H1PAHaE2?pid=ImgDet&rs=1)



# [Next.js]([Next.js - React 应用开发框架 | Next.js中文网 (nextjs.cn)](https://www.nextjs.cn/))

用于 生产环境的 **React** 框架

Next.js 具有同类框架中最佳的“开发人员体验”和许多内置功能。列举其中一些如下：

- 直观的、基于页面的路由系统（并支持动态路由）
- 预渲染。支持在页面级的静态生成(SSG) 和服务端渲染(SSR)
- 自动代码拆分，提升页面加载速度
- 具有经过优化的预取功能的客户端路由
- 内置CSS和Sass的支持，并支持任何CSS-IN-JS库
- 开发环境支持快速刷新
- 利用 Serverless Functions 及API路由构建 API 功能
- 完全可扩展



## Pages

在 Next.js 中，一个 **page（页面）** 就是一个从 `.js`、`jsx`、`.ts` 或 `.tsx` 文件导出（export）的React组件，这些文件存放在 `pages` 目录下。每个 page（页面）都使用其文件名作为路由（route）。

**示例**： 如果你创建了一个命名为 `pages/about.js` 的文件并导出（export）一个如下所示的 React 组件，则可以通过 `/about` 路径进行访问。

```jsx
function About() {
  return <div>About</div>
}
export default About
```

Next.js 支持具有动态路由的 pages（页面）。例如，如果你创建了一个命名为 `pages/posts/[id].js` 的文件，那么就可以通过 `posts/1`、`posts/2` 等类似的路径进行访问。

这里的id可以从next提供的useRouter() hooks获取

```tsx
const router = useRouter()
const id = router.query['id']
```



404页面可以直接在pages目录下添加，一旦页面not found，Next.js会自动路由到404页面。



## [预渲染](https://www.nextjs.cn/docs/basic-features/pages#预渲染)

默认情况下，Next.js 将 **预渲染** 每个 page（页面）。这意味着 Next.js 会预先为每个页面生成 HTML 文件，而不是由客户端 JavaScript 来完成。预渲染可以带来更好的性能和 SEO 效果。

每个生成的 HTML 文件都与该页面所需的最少 JavaScript 代码相关联。当浏览器加载一个 page（页面）时，其 JavaScript 代码将运行并使页面完全具有交互性。（此过程称为 *水合（hydration）*）

[两种形式的预渲染](https://www.nextjs.cn/docs/basic-features/pages#两种形式的预渲染)

Next.js 具有两种形式的预渲染： **静态生成（Static Generation）** 和 **服务器端渲染（Server-side Rendering）**。这两种方式的不同之处在于为 page（页面）生成 HTML 页面的 **时机** 。

- [**静态生成 （推荐）**](https://www.nextjs.cn/docs/basic-features/pages#static-generation-recommended)：HTML 在 **构建时** 生成，并在每次页面请求（request）时重用。
- [**服务器端渲染**](https://www.nextjs.cn/docs/basic-features/pages#server-side-rendering)：在 **每次页面请求（request）时** 重新生成 HTML。

重要的是，Next.js 允许为每个页面 **选择** 预渲染的方式。可以创建一个 “混合渲染” 的 Next.js 应用程序：对大多数页面使用“静态生成”，同时对其它页面使用“服务器端渲染”。

出于性能考虑，相对服务器端渲染，更 **推荐** 使用 **静态生成** 。 CDN 可以在没有额外配置的情况下缓存静态生成的页面以提高性能。但是，在某些情况下，服务器端渲染可能是唯一的选择。

还可以将 **客户端渲染** 与静态生成或服务器端渲染一起使用。

### [需要获取数据的静态生成SSG](https://www.nextjs.cn/docs/basic-features/pages#需要获取数据的静态生成)

某些页面需要获取外部数据以进行预渲染。有两种情况，一种或两种都可能适用。在每种情况下，你都可以使用 Next.js 所提供的以下函数：

1. 您的页面 **内容** 取决于外部数据：使用 `getStaticProps`。
2. 你的页面 **paths（路径）** 取决于外部数据：使用 `getStaticPaths` （通常还要同时使用 `getStaticProps`）。

不过静态生成的页面只会在打包时请求数据，如果后续数据更新，在前端重新打包明显是不合理的，Next.js便提供了`revalidate`选项，这样便可以进行 ISG（增量静态再生）

```tsx
export const getStaticProps = () => {
  console.log('getStaticProps again...')
  const posts = '这是一个外部数据'
  return {
    props: {
      posts
    },
    revalidate: 10 //两次访问页面的时间间隔大于10s，便会更新请求
  }
}
```

此外还提供了 notFound、redirect选项，用于处理无数据和重定向问题。



### [服务器端渲染SSR](https://www.nextjs.cn/docs/basic-features/pages#服务器端渲染)

> 也被称为 “SSR” 或 “动态渲染”。

如果 page（页面）使用的是 **服务器端渲染**，则会在 **每次页面请求时** 重新生成页面的 HTML 。

要对 page（页面）使用服务器端渲染，你需要 `export` 一个名为 `getServerSideProps` 的 `async` 函数。服务器将在每次页面请求时调用此函数。

例如，假设你的某个页面需要预渲染频繁更新的数据（从外部 API 获取）。你就可以编写 `getServerSideProps` 获取该数据并将其传递给 `Page` ，如下所示：

```jsx
function Page({ data }) {
  // Render data...
}

// This gets called on every request
export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // Pass data to the page via props
  return { props: { data } }
}

export default Page
```

如你所见，`getServerSideProps` 类似于 `getStaticProps`，但两者的区别在于 `getServerSideProps` 在每次页面请求时都会运行，而在构建时不运行。



## API路由（自己的后端）

`pages/api` 目录下的任何文件都将作为 API 端点映射到 `/api/*`，而不是 `page`。这些文件只会增加服务端文件包的体积，而不会增加客户端文件包的大小。

例如，以下 API 路由 `pages/api/user.js` 返回 `json` 格式的数据，并且状态码为 `200`：

```js
export default function handler(req, res) {
  res.status(200).json({ name: 'John Doe' })
}
```

为了使 API 路由能正常工作，你需要导出（export）一个默认函数（即 **请求处理器**），并且该函数能够接收以下参数：

- `req`: 一个 [http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage) 实例，以及一些 [预先构建的中间件](https://www.nextjs.cn/docs/api-routes/api-middlewares)
- `res`: 一个 [http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse) 实例，以及一些 [辅助函数](https://www.nextjs.cn/docs/api-routes/response-helpers)

要处理 API 路由的不同 HTTP 方法，可以在请求处理器中使用 `req.method`，如下所示：

```js
export default function handler(req, res) {
  if (req.method === 'POST') {
    // Process a POST request
  } else {
    // Handle any other HTTP method
  }
}
```

> 大部分时间应该是用不到的，因为当前前后端分离是开发的主流，但个人开发，或小型项目是可以使用的。
>
> Next项目倒是很适合部署小型的Serverless应用



另外Next.js做了很多优化，例如对于image组件提供了自己的 next/image 支持大量对于图片展示的optimize

此处不做过多记录...

# [Vue3](https://cn.vuejs.org/)

渐进式 JavaScript 框架

Vue 的组件可以按两种不同的风格书写：**选项式 API** 和**组合式 API**。

Vue3鼓励使用组合式API



## API风格

**选项式 API (Options API)**

使用选项式 API，我们可以用包含多个选项的对象来描述组件的逻辑，例如 `data`、`methods` 和 `mounted`。选项所定义的属性都会暴露在函数内部的 `this` 上，它会指向当前的组件实例。

```vue
<script>
export default {
  // data() 返回的属性将会成为响应式的状态
  // 并且暴露在 `this` 上
  data() {
    return {
      count: 0
    }
  },
  // methods 是一些用来更改状态与触发更新的函数
  // 它们可以在模板中作为事件监听器绑定
  methods: {
    increment() {
      this.count++
    }
  },
  // 生命周期钩子会在组件生命周期的各个不同阶段被调用
  // 例如这个函数就会在组件挂载完成后被调用
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
}
</script>
<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```



**组合式 API (Composition API)**

通过组合式 API，我们可以使用导入的 API 函数来描述组件逻辑。在单文件组件中，组合式 API 通常会与`setup`搭配使用。这个 `setup` attribute 是一个标识，告诉 Vue 需要在编译时进行一些处理，让我们可以更简洁地使用组合式 API。比如，`<script setup>` 中的导入和顶层变量/函数都能够在模板中直接使用。

下面是使用了组合式 API 与 `<script setup>` 改造后和上面的模板完全一样的组件：

```vue
<script setup>
import { ref, onMounted } from 'vue'
// 响应式状态
const count = ref(0)
// 用来修改状态、触发更新的函数
function increment() {
  count.value++
}
// 生命周期钩子
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>
<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```



- 在生产项目中：
  - 当你不需要使用构建工具，或者打算主要在低复杂度的场景中使用 Vue，例如渐进增强的应用场景，推荐采用选项式 API。
  - 当你打算用 Vue 构建完整的单页应用，推荐采用组合式 API + 单文件组件。



## 声明响应式状态



我们可以使用 [`reactive()`](https://cn.vuejs.org/api/reactivity-core.html#reactive) 函数创建一个响应式对象或数组：

```js
import { reactive } from 'vue'
const state = reactive({ count: 0 })
```

响应式对象其实是 [JavaScript Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)，其行为表现与一般对象相似。不同之处在于 Vue 能够跟踪对响应式对象属性的访问与更改操作

要在组件模板中使用响应式状态，需要在 `setup()` 函数中定义并返回。

**`reactive()` 的局限性**

`reactive()` API 有两条限制：

1. 仅对对象类型有效（对象、数组和 `Map`、`Set` 这样的[集合类型](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#使用键的集合对象)），而对 `string`、`number` 和 `boolean` 这样的 [原始类型](https://developer.mozilla.org/zh-CN/docs/Glossary/Primitive) 无效。

2. 因为 Vue 的响应式系统是通过属性访问进行追踪的，因此我们必须始终保持对该响应式对象的相同引用。这意味着我们不可以随意地“替换”一个响应式对象，因为这将导致对初始引用的响应性连接丢失：

   ```js
   let state = reactive({ count: 0 })
   // 上面的引用 ({ count: 0 }) 将不再被追踪（响应性连接已丢失！）
   state = reactive({ count: 1 })
   ```

同时这也意味着当我们将响应式对象的属性赋值或解构至本地变量时，或是将该属性传入一个函数时，我们会失去响应性：

```js
const state = reactive({ count: 0 })

// n 是一个局部变量，同 state.count
// 失去响应性连接
let n = state.count
// 不影响原始的 state
n++

// count 也和 state.count 失去了响应性连接
let { count } = state
// 不会影响原始的 state
count++

// 该函数接收一个普通数字，并且
// 将无法跟踪 state.count 的变化
callSomeFunction(state.count)
```



`reactive()` 的种种限制归根结底是因为 JavaScript 没有可以作用于所有值类型的 “引用” 机制。为此，Vue 提供了一个 [`ref()`](https://cn.vuejs.org/api/reactivity-core.html#ref) 方法来允许我们创建可以使用任何值类型的响应式 **ref**

```js
import { ref } from 'vue'
const count = ref(0)
```

`ref()` 将传入参数的值包装为一个带 `.value` 属性的 ref 对象：

```js
const count = ref(0)
console.log(count) // { value: 0 }
console.log(count.value) // 0
count.value++
console.log(count.value) // 1
```



## 生命周期

每个 Vue 组件实例在创建时都需要经历一系列的初始化步骤，比如设置好数据侦听，编译模板，挂载实例到 DOM，以及在数据改变时更新 DOM。在此过程中，它也会运行被称为生命周期钩子的函数，让开发者有机会在特定阶段运行自己的代码。

常用的生命周期钩子有 `onMounted` `onUpdated` `onUnmounted` 

<img src="https://cn.vuejs.org/assets/lifecycle.16e4c08e.png" alt="组件生命周期图示" style="zoom:33%;" />



## 侦听器 watch/watchEffect

在组合式 API 中，我们可以使用 [`watch` 函数](https://cn.vuejs.org/api/reactivity-core.html#watch)在每次响应式状态发生变化时触发回调函数

`watch` 的第一个参数可以是不同形式的“数据源”：它可以是一个 ref (包括计算属性)、一个响应式对象、一个 getter 函数、或多个数据源组成的数组

但是不能直接侦听响应式对象的属性值：

```js
const obj = reactive({ count: 0 })
// 错误，因为 watch() 得到的参数是一个 number
watch(obj.count, (count) => {
  console.log(`count is: ${count}`)
})
```

这里需要用一个返回该属性的 getter 函数：

```js
// 提供一个 getter 函数
watch(
  () => obj.count,
  (count) => {
    console.log(`count is: ${count}`)
  }
)
```

直接给 `watch()` 传入一个响应式对象，会隐式地创建一个深层侦听器——该回调函数在所有嵌套的变更时都会被触发，相比之下，一个返回响应式对象的 getter 函数，只有在返回不同的对象时，才会触发回调。可以显式地加上`deep`选项，强制更改为深层侦听。



`watch` 和 `watchEffect` 都能响应式地执行有副作用的回调。它们之间的主要区别是追踪响应式依赖的方式：

- `watch` 只追踪明确侦听的数据源。它不会追踪任何在回调中访问到的东西。另外，仅在数据源确实改变时才会触发回调。`watch` 会避免在发生副作用时追踪依赖，因此，我们能更加精确地控制回调函数的触发时机。
- `watchEffect`，则会在副作用发生期间追踪依赖。它会在同步执行过程中，自动追踪所有能访问到的响应式属性。这更方便，而且代码往往更简洁，但有时其响应性依赖关系会不那么明确。



**回调触发的时机**

当你更改了响应式状态，它可能会同时触发 Vue 组件更新和侦听器回调。

默认情况下，用户创建的侦听器回调，都会在 Vue 组件更新**之前**被调用。这意味着你在侦听器                  回调中访问的 DOM 将是被 Vue 更新之前的状态。

如果想在侦听器回调中能访问被 Vue 更新**之后**的 DOM，你需要指明 `flush: 'post'` 选项：

```js
watch(source, callback, {
  flush: 'post'
})
watchEffect(callback, {
  flush: 'post'
})
```

后置刷新的 `watchEffect()` 有个更方便的别名 `watchPostEffect()`：

```js
import { watchPostEffect } from 'vue'
watchPostEffect(() => {
  /* 在 Vue 更新后执行 */
})
```



**停止侦听**

在 `setup()` 或 `<script setup>` 中用**同步语句**创建的侦听器，会自动绑定到宿主组件实例上，并且会在宿主组件卸载时自动停止。

如果用异步回调创建侦听器，那么ta不会绑定到当前组件上，必须要手动停止侦听，否则会造成内存泄露。

要手动停止一个侦听器，请调用 `watch` 或 `watchEffect` 返回的函数：

```js
const unwatch = watchEffect(() => {})
// ...当该侦听器不再需要时
unwatch()
```

注意，需要异步创建侦听器的情况很少，请尽可能选择同步创建。如果需要等待一些异步数据，你可以使用条件式的侦听逻辑：

```js
// 需要异步请求得到的数据
const data = ref(null)
watchEffect(() => {
  if (data.value) {
    // 数据加载后执行某些操作...
  }
})
```



## 组合式函数

组合式函数(Composables) 是一个利用 Vue 的组合式 API 来封装和复用**有状态逻辑**的函数。

个人理解就是基于Vue提供的组合式API提取出的复用性高的工具函数

在组合使用逻辑与使用上和React的hooks高度相似，（甚至约定组合式函数以use开头useXXX）

不过 Vue文档中专门做了澄清 

“Vue 的组合式函数是基于 Vue 细粒度的响应性系统，这和 React hooks 的执行模型有本质上的不同”

[组合式 API 常见问答 | Vue.js (vuejs.org)](https://cn.vuejs.org/guide/extras/composition-api-faq.html#comparison-with-react-hooks)  

（React有的我也有，我的底层逻辑更优秀，并且可以解决React hooks存在的一些问题：人无我有，人有我优）

React Hooks的问题

1. React hooks有严格的调用顺序，且不能写在条件分支中

2. React组件中定义的变量使用钩子函数闭包捕获，如果传入错误的依赖会导致整个崩溃，“不够智能”，保持正确的代价过大，开发者很依赖eslint

3. 昂贵的计算需要依赖useMemo，传错依赖一样会有很大的问题

4. 默认情况下传递给子组件的事件处理函数会导致子组件不必要地更新，需要显示地调用useCallback优化。这个优化同样需要正确的依赖数组，并且几乎在任何时候都需要。忽视这一点会导致默认情况下对应用进行过度渲染，并可能在不知不觉中导致性能问题

   

Vue组合式函数的优势

1. Vue不用担心闭包变量问题，组合式API也不用担心调用顺序问题，还可以有条件地进行调用
2. Vue 的响应性系统运行时会自动收集计算属性和侦听器的依赖，因此无需手动声明依赖
3. 无需手动缓存回调函数来避免不必要的组件更新。Vue 细粒度的响应性系统能够确保在绝大部分情况下组件仅执行必要的更新。对 Vue 开发者来说几乎不怎么需要对子组件更新进行手动优化。



## Vue3全家桶

Vue-router（Vue2/3）路由管理

Pinia（Vue3）全局状态管理  Vuex

Vite(Vue/React/Svelte) 工具链 打包 开发服务 基于Rollup

Nuxt3（SSR） 不只是SSR，对Vue3的又一层包装 Vue3的框架，更加方便地进行SSR（服务端渲染），有利于进行SEO（搜索引擎优化），预渲染（加载优化），甚至可以集成服务端进来，操作数据库，基于Node直接在Nuxt中前后端交互（前后端又不分离了🥲） 要不还是回去写php？



# [Vite]([Vite | 下一代的前端工具链 (vitejs.dev)](https://cn.vitejs.dev/))

🙃🙃🙃没怎么研究，写进来只是为了说明自己看了

继推出Vue卷React之后，尤雨溪又推出了Vite来卷Webpack

Vite自称下一代前端工具链 不过Webpack的作者已经又推出了 Turbopack 来卷Vite了

没有对其具体原理做深入了解，但在使用Vite搭建的项目中可以明显地体会到其高效的热重载，是真的很快。另外Vite天然支持引入`.ts`文件 ，对 TypeScript、JSX、CSS 等支持开箱即用。

- 快速的冷启动
- 即时的模块热更新
- 真正的按需编译

目前Vite天然支持了Vue、React、Svelte



# [Nuxt3]([Nuxt 3 - 中文文档 (nuxtjs.org.cn)](https://www.nuxtjs.org.cn/))

这里先放个图

<img src="https://pic1.zhimg.com/v2-d71f5670d6b8bef77ba1faf3d9b6eaa0_xld.png" alt="img" style="zoom: 50%;" />

混合式Vue框架（感觉不如Next.js🤗，名字看着都像“借鉴”的，不过Nuxt的维护团队很nice，回issue很及时）



## 安装

```sh
npx nuxi init nuxt3-app
```

这里有坑，大概率报错🙃

由于国内的GFW，这个域名受到了DNS污染，没法连接。但即使是科学上网也是大概率挂掉的。

查找原因后了解到 nuxt的脚手架 nuxi 使用了 giget 来从nuxt项目模板仓库中获取文件。

giget干的事情很简单，就是利用node从github上拉取相应仓库。实际上giget貌似是nuxt团队对另一个相似的项目degit的仿制。

两者都可以用方便的命令从github拉取仓库。

唯一的不同就是degit支持自动从环境变量中获取https_proxy进行代理，而giget完全没有考虑这一点😅

github上已经有人提了pr 并且开发团队已采纳 不过我在这之后安装还是会报错。

试了网上不同的解决方法，目前有用的是 直接去修改本地的hosts文件，在可以稳定ping通raw.githubusercontent.com后再运行安装命令 概率安装成功。



## 获取数据

因为`Nuxt3`是`SSR`的方案，所以你可能不仅仅只是想要在浏览器端发送请求获取数据，还想在服务器端就获取到数据并渲染组件。

`Nuxt3`提供了 4 种方式使得你可以在服务器端异步获取数据

- useAsyncData
- useLazyAsyncData （useAsyncData+lazy:true）
- useFetch
- useLazyFetch （useFetch+lazy:true）

> 注意：他们只能在**`setup`**或者是`生命周期钩子`中使用

### useAsyncData

- 项目中可以在`pages`目录、`components`目录和`plugins`目录下使用 useAsyncData 异步获取数据

  ```ts
  //用法
  const {
    data: Ref<DataT>,// 返回的数据结果
    pending: Ref<boolean>,// 是否在请求状态中
    refresh: (force?: boolean) => Promise<void>,// 强制刷新数据
    error?: any // 请求失败返回的错误信息
  } = useAsyncData(
    key: string, // 唯一键，确保相同的请求数据的获取和去重
    fn: () => Object,// 一个返回数值的异步函数
    options?: { lazy: boolean, server: boolean }
    // options.lazy,是否在加载路由后才请求该异步方法，默认为false
    // options.server,是否在服务端请求数据，默认为true
    // options.default，异步请求前设置数据data默认值的工厂函数（对lazy:true选项特别有用）
    // options.transform，更改fn返回结果的函数
    // options.pick，只从数组中指定的key进行缓存
  )
  ```

> 可以考虑设置 lazy:true 配合 loading 状态加载器的方式，能够带来更好的用户体验。

```ts
// server/api/count.ts-例子
let counter = 0
export default () => {
  counter++
  return JSON.stringify(counter)
}
// app.vue-例子
<script setup>
const { data } = await useAsyncData('count', () => $fetch('/api/count'))
</script>

<template>
  Page visits: {{ data }}
</template>
```



### useFetch

在`pages`目录、`components`目录和`plugins`目录下使用`useFetch`也同样可以获取到任意的 URL 资源。该方法实际上是对 useAsyncData 和$fetch 的封装，提供了一个更便捷的封装方法。（它会根据 URL 和 fetch 参数自动生成一个 key，同时推断出 API 的响应类型）

```ts
//useFetch用法
const {
  data: Ref<DataT>,
  pending: Ref<boolean>,
  refresh: (force?: boolean) => Promise<void>,
  error?: any
} = useFetch(url: string, options?)

//可以看到useFetch和useAsyncData的返回对象是一样的，useFetch传参更便捷，不需要在fn中手动使用$fetch
useAsyncData(key: string,fn: () => Object,options?: { lazy: boolean, server: boolean })
//例子
<script setup>
const { data } = await useFetch('/api/count')
</script>

<template>
  Page visits: {{ data.count }}
</template>
```



受 Vue3 的影响，如果使用了`async setup()`的形式，当前组件的实例会在第一个异步操作之后丢失。如果想要使用多个异步操作，比如执行多个 useFetch，需要使用`<script setup>`或者将多个请求封装在一起请求（使用 Promise.all 将多个请求装在一起）。 **Nuxt3强烈建议使用 `<script setup>` 形式，它解决了采用[顶层 await](https://v3.cn.vuejs.org/api/sfc-script-setup.html#与普通的-script-一起使用)的限制**



### 客户端获取

> 注意：
>
> useAsyncData/useFetch默认是服务端获取数据，但其暴露出的refresh方法是客户端获取数据；
>
> 如果不想在服务端获取数据，可以在options里设置 server : false , 这样就可以在客户端获取数据；
>
> Nuxt3提供了自己的请求命令 $fetch 但我们仍可以使用 axios 来获取，方便我们从已有Vue3项目迁移。

踩坑记录：

- 使用useFetch获取数据时，发现在network里找不到请求信息

​	useFetch默认服务端获取数据，当然不会在客户端中获取到Fetch/XHR信息；如果想要在客户端请求，需要在	options中配置 `server : false`

- 使用useFetch的`refresh`在请求Nuxt的server中的api时正常使用，在请求本地node服务时报跨域错误

​	useAsyncData/useFetchde的 `refresh` 是客户端发出的请求，服务端请求是不会产生跨域问题的，但客户端	会产生跨域问题，开发时就需要服务端配置 *Access-Control-Allow-Origin*  或者前端项目配置proxy代理

- 在项目中配置前端代理proxy无效

​	Nuxt3文档中并未提及如何配置proxy，应该是默认了开发时跨域问题由服务端解决。网上搜索并没有很多                  	Nuxt3的博客，且大部分都不提设置proxy（难道全是前后端不分离用node做后端？），仅有的几个提到proxy	的博客都是说nuxt.config.ts里支持了vite，在vite属性中配置类似之前Vite的代理方式就可以了。亲测没用👍

```ts
export default defineNuxtConfig({
  vite: {
    server: {
      open: true,
      // https: true,
      proxy: {
        "/devApi": {
          target: "http://127.0.0.1:8085/",
          changeOrigin: true,
          rewrite: (path) => path.replace(/^\/devApi/, ""),
        },
      },
    },
  }
})
```

​	

后面在Nuxt3的issue里发现是要在nitro属性中设置，配置后便解决了开发跨域的问题。

```ts
export default defineNuxtConfig({
  nitro: {
    devProxy: {
      "/proxy": {
        target: "http://127.0.0.1:8085/",
        changeOrigin: true,
        prependPath: true,
      }
    }
  }
})
```



## Pages

Nuxt3的Pages对于Next的约定式文件路由做了大量的“借鉴”，包括动态路由的实现方式

但ta的使用体验个人觉得不如Next13

嵌套路由 `Nested Router`是Nuxt3的一个feature，虽然目前还没碰到必须使用嵌套路由才能实现的页面展示逻辑，可能嵌套路由实现起来比较优雅吧。

> 这里有个坑，中文文档嵌套路由的实现是引入 <NuxtChild /> 组件，但试了之后没有用。
>
> 看了github的issue后发现是已经修改为了 <NuxtPage />组件，中文文档并没有同步更新。



## Composables

Nuxt3提供的专门 “放” Vue3 的混合式函数的目录



## Middleware

Nuxt3提供了路由中间件的概念，可以在整个应用使用，目的是在导航到某一个页面之前，执行一些代码。最常见的路由守卫就可以用这个实现。

### 中间件的基本格式

一个最简单的中间件，就是在控制台打印`来的页面`，和`要去的页面`。目的是通过最简单的实例来了解中间件的基本格式。 在项目根目录，新建一个`middleware`的文件夹，然后在文件下边新建一个文件`default.global.ts` 的文件。其中的`.global`代表这个中间件是全局的，也就是在每次跳转都会执行下面的代码。

```ts
export default defineNuxtRouteMiddleware((to, from) => {
  console.log("要去那个页面:"+to.path)
  console.log("来自那个页面:"+from.path)
})
```

### 通过中间件 设置路由守卫

当我们了解路由中间件的基本写法后，在增加一些难度，来模仿一下路由守卫。比如我们要访问的页面是 http://localhost:3000/demo1，现在设置路由守卫，不允许访问，而是跳回到首页。那代码就可以写成下面的样子:

```ts
export default defineNuxtRouteMiddleware((to, from) => {
  if (to.path === '/demo1') {
     console.log('禁止访问这个页面')
     abortNavigation()  //停止当前导航，可以使用error进行报错
     return  navigateTo('/')
  }
})
```

这时候再到浏览器访问`demo1` 页面，已经不能访问了，但其他页面是可以访问的。

### 只对一个页面起作用

上面都是对所有路由起作用的，如果只想中间件对一个特殊页面起作用，也是可以的。只要去掉`.global`的后缀就是可以的。 在`middleware` 文件夹下，新建一个页面，`default.ts`，并编写下面的代码：

```ts
export default defineNuxtRouteMiddleware((to, from) => {
  console.log("Hello mybj123.com")
})
```

这时候它对任何页面都是不起作用的，你需要再去对应的页面里注册一下。去`pages`文件夹，新建一个文件`demo7.vue`。然后需要注册这个页面使用这个中间件，代码如下：

```vue
<template>
  <div class="">Demo7 Page</div>
</template>

<script setup>
definePageMeta({
  middleware: ["default"],
  // or middleware: 'auth'
});
</script>

<style scoped></style>
```

这样就对这个页面注册了一个专属的导航中间件。

> Nuxt3实现路由跳转的api是 
>
> `navigateTo` (**to: RouteLocationRaw | null | undefined**,  options? : NavigateToOptions | undefined): RouteLocationRaw | Promise<void | NavigationFailure>



# [Astro]([入门指南 🚀 Astro 文档](https://docs.astro.build/zh-cn/getting-started/))

Astro 是**集多功能于一体的Web框架**，用于构建快速、以内容为中心的网站。

## 特点

[Section titled Astro是…](https://docs.astro.build/zh-cn/concepts/why-astro/#astro是)

1. [**以内容为中心** ](https://docs.astro.build/zh-cn/concepts/why-astro/#以内容为中心)：Astro 专为内容丰富的网站而设计。
2. [**服务器优先** ](https://docs.astro.build/zh-cn/concepts/why-astro/#服务器优先)：网站在服务器上渲染 HTML 时运行速度更快。
3. [**默认快速** ](https://docs.astro.build/zh-cn/concepts/why-astro/#默认快速)：在 Astro 中构建缓慢的网站是不可能的。
4. [**易于使用** ](https://docs.astro.build/zh-cn/concepts/why-astro/#易于使用)：您不需要成为专家即可使用 Astro 构建某些内容。
5. [**功能齐全且灵活** ](https://docs.astro.build/zh-cn/concepts/why-astro/#功能齐全且灵活)：超100多种 Astro 集成可供选择。

> 个人观点：Astro并不是类似React、Vue、Svelte这样的JavaScript框架，相反他主张干掉js，全部生成静态页面，达到快速启动，不过也是可以运行js的，可以做SSR这些功能。更像是一个HTML生成器，ta以内容为中心，十分适合文档网站、博客、个人作品集这类不需要太多交互，更关注内容的网站。可以说是搭建个人博客网站的神器！



## 多页面应用（MPA）

Astro 是一个 MPA 框架。 然而，Astro 也不同于其他 MPA 框架。 它的主要区别在于它使用 JavaScript 作为其服务器语言和运行时。 传统的 MPA 框架会让您在服务器上编写不同的语言（Ruby、PHP 等）并在浏览器上编写 JavaScript。 在 Astro 中，您总是只是在编写 JavaScript、HTML 和 CSS。 这样，您可以在服务器和客户端上呈现您的 UI 组件（如 React 和 Svelte）。

其结果是开发人员体验很像 Next.js 和其他现代 Web 框架，但具有更传统的 MPA 站点架构的性能特征。

## Astro群岛（核心）

“Astro 群岛“指的是静态 HTML 中的交互性的 UI 组件。一个页面上可以有多个岛屿，并且每个岛屿都被独立呈现。你可以将它们想象成在一片由静态（不可交互）的 HTML 页面中的动态岛屿。

在Astro中，你可以使用任何被支持的UI框架（比如 React, Svelte, Vue）来在浏览器中呈现群岛。你可以在一个页面中混合或拼接许多不同的框架，或者仅仅使用自己最喜欢的。

这种架构模式依赖于 partial（局部）或 selective hydration（选择性混合）技术。

**Astro 默认生成不含客户端 JavaScript 的网站。** 如果使用前端框架 [React](https://reactjs.org/)、[Preact](https://preactjs.com/)、[Svelte](https://svelte.dev/)、[Vue](https://vuejs.org/)、[SolidJS](https://www.solidjs.com/)、[AlpineJS](https://alpinejs.dev/) 或 [Lit](https://lit.dev/)，Astro 会自动提前将它们渲染为 HTML，然后再除去所有 JavaScript。这使得 Astro 创建的网站默认非常迅速，因为 Astro 帮你自动清除了所有页面上的 JavaScript。

src/pages/index.astro

```tsx
---
// 例子：在此页面使用一个静态的 React 组件，没有 JavaScript。
import MyReactComponent from '../components/MyReactComponent.jsx';
---
<!-- 100% HTML，没有 JavaScript 在这个页面上！ -->
<MyReactComponent />
```

但是有些时候，响应式的 UI 是需要客户端 JavaScript 的。你不该将整个页面做成一个像 SPA（单页面应用）一样的 JavaScript 应用，相反，Astro 允许你创建岛屿。

src/pages/index.astro

```tsx
---
// 例子：在此页面上使用一个动态 React 组件
---
<!-- 现在这个组件是可交互性的了！
  网站的其他部分仍然是静态、没有JavaScript的。 -->
<MyReactComponent client:load />
```

使用 Astro 群岛，你的大部分页面保持着纯正、轻盈的HTML和CSS。在上面的例子中，你仅仅添加了一个简单的、孤立的**可响应岛屿**，而并没有改变任何页面其他部分的代码。

Astro 群岛的最明显的好处就是性能：你网站的大部分区域都被转换为了快速、静态的 HTML，JavaScript 只为单独组件而被加载。JavaScript 是一个加载得最慢的资源。每一个字节都影响着阅读者的体验！

另一个好处是并行加载。在上面的一些假想例子中，重要性更低的图像轮播不应该阻挡更重要的页头部分的加载。这两样东西被并行加载并被分别单独组建，这表明阅读者并不需要等着更沉重的图像轮播加载完毕就可以与页头交互了。

还有更棒的：你可以准确地告诉 Astro 如何以及何时渲染每个组件。如果该图像轮播的加载成本真的很高，你可以附加一个特殊的客户端指令，告诉 Astro 仅在轮播在页面上可见时才加载它。如果用户从未看到它，它永远不会被加载。

在 Astro 中，作为开发人员，你可以明确告诉 Astro 你的页面上的哪些组件也需要在客户端浏览器中运行。Astro 只会准确地补充页面上需要交互性的内容，并将您网站的其余部分保留为静态 HTML。

## [MarkDown（十分适合文档和博客的feature）]([Markdown 🚀 Astro 文档](https://docs.astro.build/zh-cn/guides/markdown-content/#导入-markdown))）

Astro可以自动将page目录下的.md文件解析成HTML页面，还可以将.md文件提取成组件，嵌入到页面当中，可以说是写笔记、写博客的利器。

### Markdown 页面

Astro 将 `/src/pages` 目录中的任一 `.md` 文件视为一个页面。将文件放在此目录或其的任何一个子目录中，则将用文件的路径名自动构建页面路由。

### Markdown 布局

[Section titled Markdown 布局](https://docs.astro.build/zh-cn/guides/markdown-content/#markdown-布局)

Markdown 页面有一个用于指定 `layout` 的特殊 frontmatter 属性，它定义了 Astro [布局组件](https://docs.astro.build/zh-cn/core-concepts/layouts/)的相对路径。该组件将包装你的 Markdown 内容，提供页面骨架和任何其他包含的页面模板元素。

```tsx
---
layout: ../layouts/BaseLayout.astro
---
```

Markdown 页面指定布局的方式有：

1. 通过 content 属性访问 Markdown 页面的 frontmatter 数据。
2. [``](https://docs.astro.build/zh-cn/core-concepts/astro-components/#插槽) 将指定 Markdown 内容的默认显示位置。

src/layouts/BaseLayout.astro

```tsx
---
// 1. The content prop gives access to frontmatter data
const { content } = Astro.props;
---
<html>
  <head>
    <!-- Add other Head elements here, like styles and meta tags. -->
    <title>{content.title}</title>
  </head>
  <body>
    <!-- Add other UI components here, like common headers and footers. -->
    <h1>{content.title} by {content.author}</h1>
    <!-- 2. Rendered HTML will be passed into the default slot. -->
    <slot />
    <p>Written on: {content.date}</p>
  </body>
</html>
```

`content` 属性还包含一个 `astro` 属性，其中包含有关页面的其他元数据，例如完整的 Markdown `source` 和 `headers` 对象。

一个示例博客文章 `content` 对象，类似于下方示例：

```json
{
  /** Frontmatter from a blog post
  "title": "Astro 0.18 Release",
  "date": "Tuesday, July 27 2021",
  "author": "Matthew Phillips",
  "description": "Astro 0.18 is our biggest release since Astro launch.",
  "draft": false,
  "keywords": ["astro", "release", "announcement"]
  **/
  "astro": {
    "headers": [
      {
        "depth": 1,
        "text": "Astro 0.18 Release",
        "slug": "astro-018-release"
      },
      {
        "depth": 2,
        "text": "Responsive partial hydration",
        "slug": "responsive-partial-hydration"
      }
      /* ... */
    ],
    "source": "# Astro 0.18 Release\nA little over a month ago, the first public beta [...]"
  },
  "url": ""
}
```

> 💡 `content` 属性中的 `astro` 和 `url` 是唯一受到 Astro 保护的属性。对象的其余部分则由你的 frontmatter 变量定义。



**你甚至可以在MarkDown当中使用变量和嵌入组件**



**本人已经尝试了，使用Netlify部署一个最简单的Astro搭建的博客，真的好用！**



https://hyacinth-ju.netlify.app/













