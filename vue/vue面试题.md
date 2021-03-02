# Vue 面试题

## 1. 对于MVVM的理解

MVVM 是 Model-View-ViewModel 的缩写。Model 代表数据模型，也可以在 Model 中定义数据修改和操作的业务逻辑。View 代表 UI 组件，它负责将数据模型转化成 UI 展现出来。ViewModel 监听模型数据的改变，控制视图行为，处理用户交互，简单理解就是一个同步 View 和 Model 的对象。在 MVVM 架构下，View 和 Model 之间并没有直接联系，而是通过 ViewModel 进行交互，View 和 Model 之间的交互是双向的，因此 View 数据的改变会同步到 Model 中，而 Model 数据的改变也会立即反应到 View 上。ViewModel 通过双向数据绑定把 View 层 和 Model 层连接起来，因此，开发者只需关注业务逻辑，不需要手动操作 DOM，不需要关注数据状态的同步问题。

## 2. Vue 组件间的参数传递

1. 父组件传给子组件：通过Props传递
2. 子组件传给父组件：$emit方法
3. 兄弟组件间传值：eventBus，就是创建一个事件中心，相当于中转站，可以用它来传递事件和接受事件。

## 3.v-show 与 v-if 的区别

1. v-show 是 css 的切换，v-if 是完整的销毁和创新创建
2. 频繁切换时用 v-show，运行时较少改变时用 v-if
3. v-if 是条件渲染，为 false 时不会渲染

## 4. v-for 与 v-if 哪个的优先级更高

1. v-for 的优先级更高，因为在编译模板时，先判断 v-for，在判断 v-if 的，因此 v-for 和 v-if 同时存在时，会先进行 for 循环，然后再判断 if，这种写法会影响性能，不推荐这么写。
2. 可以在 v-for 外包一层 template 标签，先进行 if 判断，如果不需要渲染，则不会进行 for 循环
3. 如果是需要对循环的 每一项 item 进行 if 判断，这种情况应使用计算属性把不需要渲染的数据过滤掉。

## 5. Vue 的生命周期

1. new Vue() 构造函数生成 Vue 实例
2. init Event & lifecycle 事件和生命周期钩子初始化
3. beforeCreated 在实例初始化之后，数据观测和事件配置之前调用
4. init injections & reactivity 初始化 inject、provide、state属性
5. created 此时参数props、methods、data、computed、watch 已初始化，初始化先后顺序 props -> methods -> data -> computed -> watch，但 dom 树并未挂载

6. 判断是否有 el 对象
    6.1 没有，则停止编译，直到该vue实例上调用vm.$mount(el)
    6.2 有 el 对象后，判断是否有模板 template
    6.3 有 template，则将模板转换为 render 函数，通过 render 函数去渲染创建 dom 树
    6.4 没有 template，则编译 el 对象外部 html 作为模板
7. beforeMount 在挂载之前调用，render 函数首次被调用生成虚拟 dom

8. 创建 Vue 实例下的 $el (虚拟)，并将其替换成真正的 dom

9. mounted 挂载完成，dom树已经完成渲染到页面，可进行 dom 操作

10. 当数据有更新被调用时，先调用 beforeUpdate，在虚拟 dom 重新渲染补丁，以最小 dom 开支来重新渲染 dom，再调用 updated
11. 组件销毁时，先调用 beforeDestroy，然后清楚 watcher、子组件事件监听，然后再调用 destroy，组件已销毁

12. 父子组件之间生命周期钩子函数加载顺序：父beforeCreate -> 父created -> 父beforeMounted -> 子beforeCreated -> 子created -> 子beforeMount -> 子mounted -> 父mounted -> 父beforeUpdate -> 子beforeUpdate -> 子updated -> 父updated -> 父beforeDestroy -> 子beforeDestroy -> 子destroy -> 父destroy

## 6. 绑定样式 Class 的用法

1. 对象方法：```:class="{'orange': isShowOrange, 'white': isShowWhite}"```
2. 数组方法：```:class="[class1, class2]"```
3. 行内样式：```:style="{color: fontColor, fontSize: `${fontSize}px`}"```

## 7. Computed、Watch、methods之间的区别
首先，computed、watch和methods都是以函数为基础的，但各自却有不同
### computed
当页面中有些数据依赖其他数据进行变动的时候，可以使用计算属性。
一个数据受多个数据影响
### watch
用于观察和监听页面上的vue实例，一个数据影响多个数据

### methods
只有在调用的时候才执行，调用一次执行一次，而computed是具有缓存的，只要计算属性的依赖没有进行相应的数据更新，那么computed会直接从缓存中获取值，多次访问都会返回之前的计算结果。
   