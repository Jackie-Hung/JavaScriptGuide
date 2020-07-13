## Promise(A+规范)、then、all


### 一、Promise(A+规范)
#### 术语:
promise 是一个有then方法的对象或者是函数，行为遵循本规范
thenable 是一个有then方法的对象或者是函数
value 是promise状态成功时的值，包括 undefined/thenable或者是 promise
exception 是一个使用throw抛出的异常值
reason 是promise状态失败时的值

#### 要求：
（1）一个promise必须有3个状态，pending，fulfilled(resolved)，rejected当处于pending状态的时候，可以转移到fulfilled(resolved)或者rejected状态。当处于fulfilled(resolved)状态或者rejected状态的时候，就不可变。
promise英文译为承诺，也就是说promise的状态一旦发生改变，就永远是不可逆的。

（2）一个promise必须有一个then方法，then方法接受两个参数：
promise.then(onFulfilled,onRejected)
其中onFulfilled方法表示状态从pending——>fulfilled(resolved)时所执行的方法，而onRejected表示状态从pending——>rejected所执行的方法。

（3）为了实现链式调用，then方法必须返回一个promise
promise2=promise1.then(onFulfilled,onRejected)

#### 代码：

### 二、
