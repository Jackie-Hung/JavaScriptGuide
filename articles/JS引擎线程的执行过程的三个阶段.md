## JS引擎线程的执行过程的三个阶段
浏览器首先按顺序加载由`<script>`标签分割的js代码块，加载js代码块完毕后，立刻进入以下三个阶段，然后再按顺序查找下一个代码块，再继续执行以下三个阶段，无论是外部脚本文件（不异步加载）还是内部脚本代码块，都是一样的原理，并且都在同一个全局作用域中。

JS引擎线程的执行过程的三个阶段：

- 语法分析
- 预编译阶段
- 执行阶段

### 一. 语法分析
分析该js脚本代码块的语法是否正确，如果出现不正确，则向外抛出一个语法错误（SyntaxError），停止该js代码块的执行，然后继续查找并加载下一个代码块；如果语法正确，则进入预编译阶段。

下面阶段的代码执行不会再进行语法校验，语法分析在代码块加载完毕时统一检验语法。

### 二. 预编译阶段
#### 1. js的运行环境
全局环境（JS代码加载完毕后，进入代码预编译即进入全局环境）

函数环境（函数调用执行时，进入该函数环境，不同的函数则函数环境不同）

eval（不建议使用，会有安全，性能等问题）

每进入一个不同的运行环境都会创建一个相应的执行上下文（Execution Context），那么在一段JS程序中一般都会创建多个执行上下文，js引擎会以栈的方式对这些执行上下文进行处理，形成函数调用栈（call stack），栈底永远是全局执行上下文（Global Execution Context），栈顶则永远是当前执行上下文。

#### 2. 函数调用栈/执行栈
调用栈，也叫执行栈，具有LIFO（后进先出）结构，用于存储在代码执行期间创建的所有执行上下文。

首次运行JS代码时，会创建一个全局执行上下文并Push到当前的执行栈中。每当发生函数调用，引擎都会为该函数创建一个新的函数执行上下文并Push到当前执行栈的栈顶。

当栈顶函数运行完成后，其对应的函数执行上下文将会从执行栈中Pop出，上下文控制权将移到当前执行栈的下一个执行上下文。

```javascript 
var a = 'Hello World!';
 
function first() { 
  console.log('Inside first function'); 
  second(); 
  console.log('Again inside first function'); 
}
 
function second() { 
  console.log('Inside second function'); 
}
 
first(); 
console.log('Inside Global Execution Context');
 
// Inside first function
// Inside second function
// Again inside first function
// Inside Global Execution Context

```

![Alt text](https://github.com/Jackie-Hung/JavaScriptGuide/blob/master/pictures/1593875976837.png)

#### 3. 执行上下文的创建
执行上下文可理解为当前的执行环境，与该运行环境相对应，具体分类如上面所说分为全局执行上下文和函数执行上下文。创建执行上下文的三部曲：

- 创建变量对象（Variable Object）

- 建立作用域链（Scope Chain）

- 确定this的指向

##### 3.1 创建变量对象
![Alt text](https://github.com/Jackie-Hung/JavaScriptGuide/blob/master/pictures/1593876064893.png)

- 创建arguments对象：检查当前上下文中的参数，建立该对象的属性与属性值，仅在函数环境(非箭头函数)中进行，全局环境没有此过程

- 检查当前上下文的函数声明：按代码顺序查找，将找到的函数提前声明，如果当前上下文的变量对象没有该函数名属性，则在该变量对象以函数名建立一个属性，属性值则为指向该函数所在堆内存地址的引用，如果存在，则会被新的引用覆盖。

- 检查当前上下文的变量声明：按代码顺序查找，将找到的变量提前声明，如果当前上下文的变量对象没有该变量名属性，则在该变量对象以变量名建立一个属性，属性值为undefined；如果存在，则忽略该变量声明

函数声明提前和变量声明提升是在创建变量对象中进行的，且函数声明优先级高于变量声明。具体是如何函数和变量声明提前的可以看后面。

创建变量对象发生在预编译阶段，但尚未进入执行阶段，该变量对象都是不能访问的，因为此时的变量对象中的变量属性尚未赋值，值仍为undefined，只有进入执行阶段，变量对象中的变量属性进行赋值后，变量对象（Variable Object）转为活动对象（Active Object）后，才能进行访问，这个过程就是VO –> AO过程。

##### 3.2 建立作用域链
通俗理解，作用域链由当前执行环境的变量对象（未进入执行阶段前）与上层环境的一系列活动对象组成，它保证了当前执行环境对符合访问权限的变量和函数的有序访问。

可以通过一个例子简单理解：

```javascript 
var num = 30;
 
function test() {
    var a = 10;
 
    function innerTest() {
        var b = 20;
 
        return a + b
    }
 
    innerTest()
}
 
test()

```
　　

 
在上面的例子中，当执行到调用innerTest函数，进入innerTest函数环境。全局执行上下文和test函数执行上下文已进入执行阶段，innerTest函数执行上下文在预编译阶段创建变量对象，所以他们的活动对象和变量对象分别是AO(global)，AO(test)和VO(innerTest)，而innerTest的作用域链由当前执行环境的变量对象（未进入执行阶段前）与上层环境的一系列活动对象组成，如下：

```javascript 
innerTestEC = {
 
    //变量对象
    VO: {b: undefined},
 
    //作用域链
    scopeChain: [VO(innerTest), AO(test), AO(global)], 
     
    //this指向
    this: window
}

```
　　

 
深入理解的话，创建作用域链，也就是创建词法环境，而词法环境有两个组成部分：

- 环境记录：存储变量和函数声明的实际位置
- 对外部环境的引用：可以访问其外部词法环境
词法环境类型伪代码如下：

```javascript 
// 第一种类型： 全局环境
GlobalExectionContext = {  // 全局执行上下文
  LexicalEnvironment: {       // 词法环境
    EnvironmentRecord: {        // 环境记录
      Type: "Object",              // 全局环境
      // 标识符绑定在这里
      outer: <null>              // 对外部环境的引用
  } 
}
 
// 第二种类型： 函数环境
FunctionExectionContext = { // 函数执行上下文
  LexicalEnvironment: {       // 词法环境
    EnvironmentRecord: {        // 环境记录
      Type: "Declarative",         // 函数环境
      // 标识符绑定在这里             // 对外部环境的引用
      outer: <Global or outer function environment reference> 
  } 
}

```
　　

在创建变量对象，也就是创建变量环境，而变量环境也是一个词法环境。在 ES6 中，词法 环境和 变量 环境的区别在于前者用于存储函数声明和变量（ let 和 const ）绑定，而后者仅用于存储变量（ var ）绑定。

如例子：
``` javascript 
let a = 20; 
const b = 30; 
var c;
 
function multiply(e, f) { 
 var g = 20; 
 return e * f * g; 
}
 
c = multiply(20, 30);
```
 
执行上下文如下所示
``` javascript 
GlobalExectionContext = {
 
  ThisBinding: <Global Object>,
 
  LexicalEnvironment: { 
    EnvironmentRecord: { 
      Type: "Object", 
      // 标识符绑定在这里 
      a: < uninitialized >, 
      b: < uninitialized >, 
      multiply: < func > 
    } 
    outer: <null> 
  },
 
  VariableEnvironment: { 
    EnvironmentRecord: { 
      Type: "Object", 
      // 标识符绑定在这里 
      c: undefined, 
    } 
    outer: <null> 
  } 
}
 
FunctionExectionContext = { 
    
  ThisBinding: <Global Object>,
 
  LexicalEnvironment: { 
    EnvironmentRecord: { 
      Type: "Declarative", 
      // 标识符绑定在这里 
      Arguments: {0: 20, 1: 30, length: 2}, 
    }, 
    outer: <GlobalLexicalEnvironment> 
  },
 
  VariableEnvironment: { 
    EnvironmentRecord: { 
      Type: "Declarative", 
      // 标识符绑定在这里 
      g: undefined 
    }, 
    outer: <GlobalLexicalEnvironment> 
  } 
}

```
变量提升的具体原因：在创建阶段，函数声明存储在环境中，而变量会被设置为 undefined（在 var 的情况下）或保持未初始化（在 let 和 const 的情况下）。所以这就是为什么可以在声明之前访问 var 定义的变量（尽管是 undefined ），但如果在声明之前访问 let 和 const 定义的变量就会提示引用错误的原因。此时let 和 const处于未初始化状态不能使用，只有进入执行阶段，变量对象中的变量属性进行赋值后，变量对象（Variable Object）转为活动对象（Active Object）后，let和const才能进行访问。

关于函数声明和变量声明，这篇文章讲的很好：https://github.com/yygmind/blog/issues/13

另外关于闭包的理解，如例子：
``` javascript 
function foo() {
    var num = 20;
 
    function bar() {
        var result = num + 20;
 
        return result
    }
 
    bar()
}
 
foo()
```
　　

 
浏览器分析如下：
![Alt text](https://github.com/Jackie-Hung/JavaScriptGuide/blob/master/pictures/1593876368531.png)

闭包

chrome浏览器理解闭包是foo，那么按浏览器的标准是如何定义闭包的，总结为三点：

- 在函数内部定义新函数

- 新函数访问外层函数的局部变量，即访问外层函数环境的活动对象属性

- 新函数执行，创建新的函数执行上下文，外层函数即为闭包

##### 3.3 this指向

xxxx （较复杂，以后再写）

### 三. 执行阶段
#### 1. 网页的线程
永远只有JS引擎线程在执行JS脚本程序，其他三个线程只负责将满足触发条件的处理函数推进事件队列，等待JS引擎线程执行， 不参与代码解析与执行。

JS引擎线程： 也称为JS内核，负责解析执行Javascript脚本程序的主线程（例如V8引擎）

事件触发线程： 归属于浏览器内核进程，不受JS引擎线程控制。主要用于控制事件（例如鼠标，键盘等事件），当该事件被触发时候，事件触发线程就会把该事件的处理函数推进事件队列，等待JS引擎线程执行

定时器触发线程：主要控制计时器setInterval和延时器setTimeout，用于定时器的计时，计时完毕，满足定时器的触发条件，则将定时器的处理函数推进事件队列中，等待JS引擎线程执行。 注：W3C在HTML标准中规定setTimeout低于4ms的时间间隔算为4ms。

HTTP异步请求线程：通过XMLHttpRequest连接后，通过浏览器新开的一个线程，监控readyState状态变更时，如果设置了该状态的回调函数，则将该状态的处理函数推进事件队列中，等待JS引擎线程执行。 注：浏览器对通一域名请求的并发连接数是有限制的，Chrome和Firefox限制数为6个，ie8则为10个。

#### 2. 宏任务
宏任务（macro-task）可分为同步任务和异步任务：

同步任务指的是在JS引擎主线程上按顺序执行的任务，只有前一个任务执行完毕后，才能执行后一个任务，形成一个执行栈（函数调用栈）。

异步任务指的是不直接进入JS引擎主线程，而是满足触发条件时，相关的线程将该异步任务推进任务队列(task queue)，等待JS引擎主线程上的任务执行完毕，空闲时读取执行的任务，例如异步Ajax，DOM事件，setTimeout等。

理解宏任务中同步任务和异步任务的执行顺序，那么就相当于理解了JS异步执行机制–事件循环（Event Loop）。

#### 3. 事件循环
事件循环可以理解成由三部分组成，分别是：

主线程执行栈

异步任务等待触发

任务队列

任务队列(task queue)就是以队列的数据结构对事件任务进行管理，特点是先进先出，后进后出。

![Alt text](https://github.com/Jackie-Hung/JavaScriptGuide/blob/master/pictures/1593876551567.png)

setTimeout和setInterval的区别：

setTimeout是在到了指定时间的时候就把事件推到任务队列中，只有当在任务队列中的setTimeout事件被主线程执行后，才会继续再次在到了指定时间的时候把事件推到任务队列，那么setTimeout的事件执行肯定比指定的时间要久，具体相差多少跟代码执行时间有关

setInterval则是每次都精确的隔一段时间就向任务队列推入一个事件，无论上一个setInterval事件是否已经执行，所以有可能存在setInterval的事件任务累积，导致setInterval的代码重复连续执行多次，影响页面性能。

#### 4. 微任务
微任务是在es6和node环境中出现的一个任务类型，如果不考虑es6和node环境的话，我们只需要理解宏任务事件循环的执行过程就已经足够了，但是到了es6和node环境，我们就需要理解微任务的执行顺序了。 微任务（micro-task）的API主要有:Promise， process.nextTick

![Alt text](https://github.com/Jackie-Hung/JavaScriptGuide/blob/master/pictures/1593876568949.png)

例子理解：

``` javascript 
console.log('script start');
 
setTimeout(function() {
  console.log('setTimeout');
}, 0);
 
Promise.resolve().then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});
 
console.log('script end');
```
　　

 
执行过程如下：

- 代码块通过语法分析和预编译后，进入执行阶段，当JS引擎主线程执行到console.log('script start');，JS引擎主线程认为该任务是同步任务，所以立刻执行输出script start，然后继续向下执行；

- JS引擎主线程执行到setTimeout(function() { console.log('setTimeout'); }, 0);，JS引擎主线程认为setTimeout是异步任务API，则向浏览器内核进程申请开启定时器线程进行计时和控制该setTimeout任务。由于W3C在HTML标准中规定setTimeout低于4ms的时间间隔算为4ms，那么当计时到4ms时，定时器线程就把该回调处理函数推进任务队列中等待主线程执行，然后JS引擎主线程继续向下执行

- JS引擎主线程执行到Promise.resolve().then(function() { console.log('promise1'); }).then(function() { console.log('promise2'); });，JS引擎主线程认为Promise是一个微任务，这把该任务划分为微任务，等待执行

- JS引擎主线程执行到console.log('script end');，JS引擎主线程认为该任务是同步任务，所以立刻执行输出script end

- 主线程上的宏任务执行完毕，则开始检测是否存在可执行的微任务，检测到一个Promise微任务，那么立刻执行，输出promise1和promise2

- 微任务执行完毕，主线程开始读取任务队列中的事件任务setTimeout，推入主线程形成新宏任务，然后在主线程中执行，输出setTimeout

最后的输出结果即为：

```text
script start
script end
promise1
promise2
setTimeout
```