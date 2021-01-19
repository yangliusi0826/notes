# Vue源码学习一
## new Vue中做了什么
我们在一个web项目执行代码```import Vue from 'vue'; let vm = new Vue({el: 'app', ...})```会发生什么呢？  
### 1.找到入口文件
 先找到入口文件 **platforms/web/entry-runtime-with-complier.js** 在这里有将Vue export 出去，那这里的Vue又是从哪里import来的呢，顺着这条线往上找，**platforms/web/runtime/index,js** -> **core/index.js** -> **core/instance/index.js** , 在**core/instance/index.js**文件中看到了Vue的构造函数
```js
// core/instance/index.js

import { initMixin } from './init'
import { stateMixin } from './state'
import { renderMixin } from './render'
import { eventsMixin } from './events'
import { lifecycleMixin } from './lifecycle'
import { warn } from '../util/index'

function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  this._init(options)
}

initMixin(Vue)
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)

export default Vue

```

### 2. Vue的构造函数
```new Vue```执行了Vue的构造函数，构造函数中调用了```this._init(options)```方法，这个方法是在```initMixin(Vue)```中添加在Vue原型上的方法。
```js
export function initMixin (Vue: Class<Component>) {
  Vue.prototype._init = function (options?: Object) {
    const vm: Component = this
    // ...
  }
}
```

### 3. _init()方法
在```_init```方法中进行了一系列的初始化，生命周期的初始化、事件的初始化等等。
```js
// expose real self
vm._self = vm
initLifecycle(vm)
initEvents(vm)
initRender(vm)
callHook(vm, 'beforeCreate')
initInjections(vm) // resolve injections before data/props
initState(vm)
initProvide(vm) // resolve provide after data/props
callHook(vm, 'created')

```

### 4. initState()方法
* ```initState(vm)```中做了什么呢，可以看到是对props、methods、data、computed和watch进行初始化。
```js
// core/instance/state.js

export function initState (vm: Component) {
  vm._watchers = []
  const opts = vm.$options
  if (opts.props) initProps(vm, opts.props)
  if (opts.methods) initMethods(vm, opts.methods)
  if (opts.data) {
    initData(vm)
  } else {
    observe(vm._data = {}, true /* asRootData */)
  }
  if (opts.computed) initComputed(vm, opts.computed)
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}
```

* 在下面的例子中，访问```props```和```data```下的属性时，可以直接通过```vm.属性名```来访问，这是为什么呢？为啥不是通过vm.props.name或者是vm.data.message来访问呢？
  ```js
  let vm = new Vue({
    el: 'app',
    props: {
      name: {
        type: String,
        default: 'tianxin'
      }
    },
    data: {
      message: 'hello'
    }
  })

  console.log(vm.name); // tianxin
  console.log(vm.message); // hello
  ```
  * 在第三步的```_init()```方法中，将构造函数中的参数```options```赋值给```vm.$option```了，所以可以通过 ```vm.$option.props.name``` 和 ```vm.$option.data.message``` 来访问 ```name``` 和 ```message``` 属性。
  * 在```initState```中初始化了 ```props``` 和 ```data``` 
  * ```initProps``` 中遍历了 ```props``` 下的所有属性，在 ```defineReactive``` 方法中将 ```props``` 下属性的key和value放到了常量 ```porps``` 下，```vm._props``` 和 ```porps``` 指向的是同一个对象，所以 ```porps``` 和 ```vm._props``` 的值是相同的（在这一步中我们可以通过 vm._props.name 来访问 name 属性）。
    ```js
    function initProps (vm: Component, propsOptions: Object) {
      const propsData = vm.$options.propsData || {}
      const props = vm._props = {}
      // cache prop keys so that future props updates can iterate using Array
      // instead of dynamic object key enumeration.
      const keys = vm.$options._propKeys = []
      const isRoot = !vm.$parent
      // root instance props should be converted
      if (!isRoot) {
        toggleObserving(false)
      }
      for (const key in propsOptions) {
        keys.push(key)
        const value = validateProp(key, propsOptions, propsData, vm)
        /* istanbul ignore else */
        if (process.env.NODE_ENV !== 'production') {
          const hyphenatedKey = hyphenate(key)
          if (isReservedAttribute(hyphenatedKey) ||
              config.isReservedAttr(hyphenatedKey)) {
            warn(
              `"${hyphenatedKey}" is a reserved attribute and cannot be used as component prop.`,
              vm
            )
          }
          defineReactive(props, key, value, () => {
            if (!isRoot && !isUpdatingChildComponent) {
              warn(
                `Avoid mutating a prop directly since the value will be ` +
                `overwritten whenever the parent component re-renders. ` +
                `Instead, use a data or computed property based on the prop's ` +
                `value. Prop being mutated: "${key}"`,
                vm
              )
            }
          })
        } else {
          defineReactive(props, key, value)
        }
        // static props are already proxied on the component's prototype
        // during Vue.extend(). We only need to proxy props defined at
        // instantiation here.
        if (!(key in vm)) {
          proxy(vm, `_props`, key)
        }
      }
      toggleObserving(true)
    }
    ```

  * 在 ``` proxy(vm, `_props`, key) ```方法中将 ```props``` 下的属性定义为 ```vm``` 一个访问器属性，```get``` 属性中返回的值是 ``` _props``` 下的属性值，```set``` 属性中设置的值也是 ``` _props``` 下的属性值，由此可以知道，我们访问 ```vm.属性名``` 时，通过代理后实际访问的是 ```vm._porps.属性名```；给 ```vm.属性名``` 设置值时，通过代理后实际是给 ```vm._porps.属性名``` 设置值。（在这一步可以知道，我们通过vm.name访问name属性时，实际上访问的是vm._props.name的值）
    ```js

    const sharedPropertyDefinition = {
      enumerable: true,
      configurable: true,
      get: noop,
      set: noop
    }
    export function proxy (target: Object, sourceKey: string, key: string) {
      sharedPropertyDefinition.get = function proxyGetter () {
        return this[sourceKey][key]
      }
      sharedPropertyDefinition.set = function proxySetter (val) {
        this[sourceKey][key] = val
      }
      Object.defineProperty(target, key, sharedPropertyDefinition)
    }
    ```
  
  * 直接通过 ```vm.message``` 来访问 data 下的 message 属性的原理跟 props 的一样的，都是通过 proxy 代理，实际访问的是 ```vm._data.message```。