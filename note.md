# 一、js 的数据类型

js 可以分为两种数据类型，基本数据类型和引用数据类型。

## 1. 基本数据类型

### 类型

基本数据类型包含 ```Undefined``` 、```Null```、```Boolean```、```Number```、```String```，还有在 ES6 中新增的 ```Symbol``` 和 ES10 中新增的 ```BigInt``` 类型。

* ```Symbol``` 代表创建后独一无二且不可变的数据类型，它的出现我认为主要是为了解决可能出现的全局变量冲突问题。
* ```BigInt``` 是一种数字类型数据，它可以表示任意精度格式的整数，使用 BigInt 可以安全地储存和操作大整数，即使这个数已经超出了 Number 能够表示的安全整数范围。

### 存储位置

基本数据类型的值是直接存储在栈（stack）中的，占据空间小，大小固定，属于被频繁使用数据。

## 2. 引用数据类型

### 类型

引用数据类型包含对象、数组和函数等。

### 存储位置

引用数据类型的值保存在堆（heap）中，占据空间大，大小不固定，通过使用在栈中保存对应的指针来获取堆中的值。

## 3. 数据类型判断的方式

### 3.1 typeof
```typeof``` 是一个操作符，其右侧跟一个一元表达式，返回这个这个表达式的数据类型。返回结果用该类型的字符串（全小写字母）形式表示，包含：number、string、undefined、boolean、symbol、object、function。
```js
typeof 'string'; // string
typeof 123; // number
typeof true; // boolean
typeof undefined; // undefined
typeof null; // object
typeof Symbol(); // symbol
typeof {}; // object
typeof function() {}; // function
typeof new Date(); // object
```
* 对于基本类型，除 null 以外，均可以返回正确的结果。
* 对于引用类型，除 function以外，一律返回 object 类型。
* 对于 null, 返回 object 类型。
* 对于 function，返回 function 类型。

### 3.2 instanceof (检测的是原型)
instanceof 是用来判断 A 是否为 B 的实例，表达式为：A instanceof B。instanceof 检测的是原型，内部机制是通过判断对象的原型链中是否有类型的原型
```js
instanceof (A, B) {
  var L = A.__proto__; // 获取对象的原型
  var R = B.prototype; // 获取类型的原型
  // 循环判断对象的原型是否等于类型的原型，直到对象原型为 null，因为原型链最终为 null
  while(true) {
    if (L === null || L === undefined) {
      return false;
    }
    if (L === R) {
      return true;
    }
    L = L.__proto__;
  }
}
```
**注意： instanceof 只能用来判断两个对象是否属于实例关系，而不能判断一个对象实例具体属于哪种类型。**  

instanceof 操作符的问题在与，它假定只有一个全局执行环境。如果网页中包含多个框架，那实际上就存在两个以上不同的全局执行环境，从而存在两个以上不同版本的构造函数。如果你从一个框架向另一个框架传入一个数组，那么传入的数组与第二个框架中原生创建的数组分别具有各自不同的构造函数。
```js
var iframe = document.createElement('iframe');
document.body.appendChild(iframe);
xArray = window.frames[0].Array; // 框架传入的
var arr = new xArray(1, 2, 3); 
arr instanceof Array; // false  Array 为本框架中原生的
```
针对数组的这个问题，ES5 提供了 Array.isArray() 方法 。该方法用以确认某个对象本身是否为 Array 类型，而不区分该对象在哪个环境中创建。  
Array.isArray() 本质上检测的是对象的 [[Class]] 值，[[Class]] 是对象的一个内部属性，里面包含了对象的类型信息，其格式为 [object Xxx] ，Xxx 就是对应的具体类型 。对于数组而言，[[Class]] 的值就是 [object Array] 。

### 3.3 constructor
当一个函数 Person 被定义时，JS 引擎会为 Person 添加 prototype 原型，然后在 prototype 上添加一个 constructor 属性，并让其指向 Person 的引用。F利用原型对象的constructor属性引用了自身，当F作为构造函数创建对象时，原型上的constructor属性被遗传到了新创建的对象上，从原型链角度讲，构造函数F就是新对象的类型。这样做的意义是，让对象诞生以后，就具有可追溯的数据类型。
```js
''.constructor === String; // true
123.constructor === Number; // true
false.constructor === Boolean; // true
new Function().constructor === Function; // true
new Date().constructor; // Date
[].constructor; // Array
```
* null 和 undefined 是无效的对象，因此是不会有 constructor 存在的，这两种类型的数据需要通过其他方式来判断。
* 函数的 constructor 是不稳定的，这个主要体现在自定义对象上，当开发者重写 prototype 后，原有的 constructor 引用会丢失，constructor 会默认为 Object
  

### 3.4 toString
toString() 是 Object 的原型方法，调用该方法，默认返回当前对象的 [[Class]] 。这是一个内部属性，其格式为 [object Xxx] ，其中 Xxx 就是对象的类型。  
对于 Object 对象，直接调用 toString()  就能返回 [object Object] 。而对于其他对象，则需要通过 call / apply 来调用才能返回正确的类型信息。
```js
Object.prototype.toString({}); // [Object Object]
Object.prototype.toString.call('') ;  // [object String]
Object.prototype.toString.call(1) ;   // [object Number]
Object.prototype.toString.call(true) ;// [object Boolean]
Object.prototype.toString.call(Symbol());//[object Symbol]
Object.prototype.toString.call(undefined) ;// [object Undefined]
Object.prototype.toString.call(null) ;// [object Null]
Object.prototype.toString.call(newFunction()) ;// [object Function]
Object.prototype.toString.call(newDate()) ;// [object Date]
Object.prototype.toString.call([]) ;// [object Array]
Object.prototype.toString.call(newRegExp()) ;// [object RegExp]
Object.prototype.toString.call(newError()) ;// [object Error]
Object.prototype.toString.call(document) ;// [object HTMLDocument]
Object.prototype.toString.call(window) ;//[object global] window 是全局对象 global 的引用
```


## 4. null 和 undefined 的区别

* ```undefined``` 和 ```null``` 的值是相等的，``` undefined == null // true```。
* ```undefined``` 和 ```null``` 转换为 ```Boolean```的值都是 ```false```。
* ```undefined``` 转换为数值时为 ```NaN```，```null``` 转换为数值时为 ```0```。
*  ```undefined``` 转换为数值时为 ```NaN```，```null``` 转换为数值时为 ```0```。
* ```undefined``` 是 ```Undefined``` 类型的值，表示“缺少值”，就是此处应该有一个值，但是还没有定义。典型用法如下：
  * 一个变量声明了但是还没有赋值时，就等于 ```undefined```。
  * 调用函数时，应该提供的参数没有提供时，该参数等于 ```undefined```。
  * 对象没有赋值的属性，该属性的值为 ```undefined```。
  * 函数没有返回值时，默认返回 ```undefined```。
* ```null``` 是 ```Null``` 类型的值，表示一个空对象指针，也就是“没有对象”（这也是使用 ```typeof``` 操作符检查 ```null``` 值时会返回 ```object``` 的原因），即该处不应该有值。典型用法如下：
     * 作为对象原型链的终点。


### undefined

* undefined代表的含义是未定义的，一般变量声明了但还没有定义的时候会返回 undefined。

## 5. 数据赋值、浅拷贝和深拷贝的区别
数据赋值、浅拷贝和深拷贝的区别，查看此[文章]((https://blog.csdn.net/yangliusi/article/details/109283434))
深拷贝实现代码如下(只考虑了对象和数组的情况)：
```js
function deepClone(originData, weakMap = new WeakMap()) {
  // 判断是否为引用类型
  if (typeof originData === 'object' ) {
    // 判断是否为数组，对象和数组初始值不一样
    let copyData = Array.isArray(originData) ? [] : {};

    // 判断容器中是否有已克隆过的数据，解决循环引用问题
    if (weakMap.get(originData)) {
      return weakMap.get(originData);
    }
    weakMap.set(originData, copyData);

    for(let prop in originData) {
      copyData[prop] = deepClone(originData[prop]);
    }
    return copyData;
  } else {
    return originData;
  }
}
```

## 6. 数据类型转换


# 二、执行上下文

当 JavaScript 代码执行一段可执行代码(excution code)时，会创建对应的执行上下文(excution content)。 

## 执行上下文栈

JavaScript 引擎创建了执行上下文栈(excution context stack, ECS)来管理执行上下文。  
JavaScript 开始要解释执行代码时，最先遇到的就是全局代码，所以初始化的时候首先就会向执行上下文栈压入一个全局执行上下文。当执行一个函数时，就会创建一个执行上下文，并且压入执行上下文栈，函数执行完毕后，就会将函数额执行上下文从执行栈中弹出。当整个应用程序结束的时候，执行栈才会被清空，所以在程序结束之前，执行栈底部永远有一个全局执行上下文。

## 执行上下文的重要属性

对于每个执行上下文，都有三个重要的属性：
* 变量对象（Variable Object, VO）
* 作用域链（Scope chain）
* this

### 变量对象

变量对象是与执行上下文相关的数据作用域，存储了在上下文中定义的变量和函数声明。
变量对象的创建过程：
1. 全局上下文的变量对象初始化时全局对象
2. 函数上下文的变量对象初始化只包含 Arguments 对象
3. 在进入执行上下文时会给变量对象添加形参，函数声明、变量声明等初始的属性值
4. 在代码执行阶段，会再次修改变量对象的属性值

```js
function foo(a) {
  var b = 2;
  function c() {}
  var d = function() {};

  b = 3;

}

foo(1);

// 在进入执行上下文后，这时候的 AO 是：
AO = {
  arguments: {
    0: 1,
    length: 1
  },
  a: 1,
  b: undefined,
  c: reference to function c() {},
  d: undefined
}

// 代码执行阶段，会顺序执行代码，根据代码，修改变量对象的值
AO = {
  arguments: {
    0: 1,
    length: 1
  },
  a: 1,
  b: 2,
  c: reference to function c() {},
  d: reference to FunctionExpression "d"
}

```

### 作用域链

当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级（词法层面上的父级）执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做作用域链。  
以一个函数的创建和激活两个时期来讲解作用域时如何创建和变化的

#### 函数创建

函数的作用域在函数定义时就决定了。  
函数有一个内部属性 \[[scope]]，当函数创建的时候，就会保存所有父变量对象到其中，\[[scope]] 就是所有父变量对象的层级链，但 \[[scope]] 不代表完整的作用域链。
```js
function foo() {
    function bar() {
        ...
    }
}

// 函数创建时，各自的 [[scope]]为：
foo.[[scope]] = [
  globalContext.VO
]
bar.[[scope]] = [
  fooContext.AO,
  globalContext.VO
]
```

#### 函数激活

当函数激活时，进入函数上下文，创建VO/AO后，就会将活动对象添加到作用域链的前端。  
这时候执行上下文的作用域链，我们命名为 Scope:
```js
Scope = [AO].concat([[scope]]);
```
至此，作用域链创建完毕。


## 举例
```js
var scope = "global scope";
function checkscope(){
    var scope2 = 'local scope';
    return scope2;
}
checkscope();
```
执行过程如下：
1. checkscope 函数被创建，保存作用域链到内部属性 \[[scope]]
    ```js
    checkscope.[[scope]] = [
      globalContext.VO
    ]
    ```
2. 执行 checkscope 函数，创建 checkscope 函数执行上下文，并将执行上下文压入执行栈
    ```js
    ECStack = [
      checkscopeContext,
      globalContext
    ]
    ```
3. checkscope 函数不立刻执行，开始做准备工作，第一步：复制函数 \[[scope]] 属性创建作用域链
    ```js
    checkscopeContext = [
      Scope: checkscope.[[scope]]
    ]
    ```
4. 第二步：用 arguments 创建活动对象，随后初始化活动对象，加入形参、函数声明、变量声明
    ```js 
    checkscopeContext = [
      AO: {
        arguments: [
          lenght: 0
        ],
        scope2: undefined
      },
      Scope: checkscope.[[scope]]
    ]
    ```
5. 第三步：将活动对象压入 checkscope 作用域链顶端
    ```js 
    checkscopeContext = [
      AO: {
        arguments: [
          lenght: 0
        ],
        scope2: undefined
      },
      Scope: [AO, checkscope.[[scope]]]
    ]
    ```
6. 准备工作做完，开始执行函数，随着函数执行，修改 AO 的属性值
    ```js 
    checkscopeContext = [
      AO: {
        arguments: [
          lenght: 0
        ],
        scope2: 'local scope'
      },
      Scope: [AO, globalContext.VO]
    ]
    ```
7. 查找到 scope2 的值，返回后函数执行完毕，函数上下文从执行上下文栈中弹出
    ```js
    ECStack = [
      globalContext
    ]
    ```

## this


# 手写轮子

## new 的模拟实现
通过 new 来创建一个对象的过程：
1. 创建一个新对象
2. 将构造函数的作用域赋给新对象（因此 this 就指向了这个新对象）
3. 执行构造函数中的代码（为这个新对象添加属性）
4. 返回新对象

```js
function myNew() {
  // 创建一个新对象
  var obj = new Object();
  // 取出传入的构造函数
  var constructor = [].shift.call(arguments);
  // 将 obj 的原型指向构造函数的原型，这样 obj 就可以访问到构造函数原型中的属性
  obj.__proto__ = constructor.prototype;
  // 执行构造函数，使用apply，改变构造函数 this 指向新的对象，这个 obj 就可以访问构造函数中的属性
  var ret = constructor.apply(obj, arguments);

  // 如果构造函数返回一个对象，在实例中只能访问返回对象中的属性，如果返回的是基本类型，没有影响。
  return typeof ret === 'object' ? ret : obj;
}
```

## call 的模拟实现
call() 方法在使用一个指定的 this 和若干个指定的参数值的前提下调用某个函数和方法。
```js
var foo = {
    value: 1
};

function bar() {
    console.log(this.value);
}

bar.call(foo); // 1
```
call主要实现了两个步骤：1. 改变 this 指向第一个参数foo；2. 调用 call 的方法 bar 执行了
实现思路： 将调用的方法 bar 设置为  第一个参数 foo 的属性，然后再执行 bar 方法, 这时 this 是指向 foo 的，执行结束后将 foo 中的属性删除。

```js
Function.prototype.myCall = function(context) {
  // this参数可以传入 null, 当为 null 时视为指向 window
  context = context || window;
  // 1. 获取调用 call 的函数，用 this 可以获取; 2.将 调用的函数设为对象的属性
  context.fn = this;
  // 获取闯入执行函数的参数
  var args = [];
  for (var i = 1; i < arguments.length; i++) {
    args.push('arguments[' + i + ']');
  }

  // 将参数传入并执行函数
  var result = eval('context.fn(' + args + ')');
  // 删除属性
  delete context.fn;
  return result;
}
```

## apply 的模拟实现
apply 与 call 的区别在与参数不一样，传入执行函数的参数，call 方法是多个值，apply 是一个数组

```js
Function.prototype.myApply = function(context, arr) {
  // this参数可以传入 null, 当为 null 时视为指向 window
  context = context || window;
  // 1. 获取调用 call 的函数，用 this 可以获取; 2.将 调用的函数设为对象的属性
  context.fn = this;

  var result;
  if (!arr) {
    result = context.fn();
  } else {
    // 获取闯入执行函数的参数
    var args = [];
    for (var i = 0; i < arr.length; i++) {
      args.push('arguments[' + i + ']');
    }

    // 将参数传入并执行函数
    result = eval('context.fn(' + args + ')');
  }

  // 删除属性
  delete context.fn;
  return result;
}
```

## bind 的模拟实现
bind 方法也是改变 this 的指向，与 call 不同的是，bind 返回的是一个函数。

```js
Function.prototype.myCall = function(context) {
  var self = this;
  var args = Array.prototype.slice.call(arguments, 1);

  var fBound = function（） {
    // 获取调用返回函数传入的参数
    var bindArgs = Array.prototype.slice.call(arguments);
    // 如果是作为构造函数，this 指向实例，所以将绑定函数的 this 指向该实例
    return self.apply(this instanceof bindArgs ? fBound : context, args.concat(bindArgs));
  }

  fBound.prototype = this.prototype;
  return fBound;
}
```