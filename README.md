# 1. 前端
## 1.1 Vue.js
### 1.1.1 初始化vue项目
1. ```npm install -g vue-cli```       全局安装vue脚手架
2. ```vue init webpack my-project```  初始化vue项目
3. ```npm install```                  安装项目依赖，把package.json需要的依赖 安装到 node_modules
4. ```npm run dev```

> vue3.0以上版本**Vue CLI**的包名称由 ```vue-cli``` 改成 ```@vue/cli``` 。如果全局安装了旧版本的```vue-cli```(1.x或2.x)，你需要先通过 ```npm uninstall vue-cli -g``` 卸载它。  
* 安装新版本的Vue CLI并初始化项目  
  * ```npm install -g @vue/cli```
  * ```vue create myproject```


### 1.1.2 vue的两种代码模式compiler和runtime
vue升级到2.0之后可能出现的报错信息
> [Vue warn]: You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.
#### 原因
* vue有两种形式的代码complier(模板)模式和runtime(运行时)模式，vue模板的package.json的main字段默认为runtime模式，指向了“dist/vue.runtime.common.js”位置。  
* 在main.js文件中，初始化vue代码如下所示，这种形式为complier模式的
```js
// complier
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```
#### 解决方法
##### 方法一
* 修改main.js文件中初始化vue代码为runtime模式，代码如下：
```js
// runtime
new Vue({
  el: '#app',
  render: h => h(App)
})
```
##### 方法二
* 在webpack.base.conf.js文件中加上vue的别名配置，代码如下
```js
resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      'vue$': 'vue/dist/vue.esm.js', //内部为正则表达式  vue结尾的
      '@': resolve('src'),
    }
  },
```
在main.js中```import Vue from 'vue'```引用的是vue/dist/vue.esm.js，直接指定了文件的位置，没有使用main字段默认的文件位置，使用的是complier模式。

## 1.2 CSS
### 1.2.1 设置input中placeholder的样式
```css
::-webkit-input-placeholder { /* Chrome/Opera/Safari */ 
  /* 样式 */
}
::-moz-placeholder { /* Firefox 19+ */  
  /* 样式 */
}
:-ms-input-placeholder { /* IE 10+ */ 
  /* 样式 */
}
:-moz-placeholder { /* Firefox 18- */ 
  /* 样式 */
}
```

#### input placeholder支持的属性
* font properties
* color
* background properties
* word-spacing
* letter-spacing
* text-decoration
* vertical-align
* text-transform
* line-height
* text-indent
* opacity