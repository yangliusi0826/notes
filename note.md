# js 的数据类型

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
