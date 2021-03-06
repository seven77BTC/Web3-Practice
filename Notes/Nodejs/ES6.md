# *require and import*

    ES6模块：import 加载
    CommonJS模块：require() 加载

    require是同步加载
    import是异步加载,独立的静态解析阶段

# *exports => require*

node中的文件和模块是一一对应的，一个nodejs文件即一个模块 including js代码，json，或编译过的 C/C++ 拓展
exports => require

*main.js*
    
    var hello = require('./hello')
    hello.world()

*hello.js*

    exports.world = function() {
        console.log('Hello World')
    }

# *module.exports => require*

*hello.js*

    function Hello() {
        var name;
        this.setName = function(thyName) {
            name = thyName;
        };
        this.sayHello = function() {
            console.log('Hello' + name);
        };
    };
    module.exports = Hello;

*main.js*

    var Hello = require('./hello');
    hello = new Hello();
    hello.setName('BYVoid');
    hell0.satHello();

# *const and const{}*

const{} is using the ES6 destructuring assignment to be shorter

    const obj = {
        name: "Fred",
        age: 42,
        id: 1
    }

    const { name } = obj;
    console.log("name", name);

    const { age, id } = obj;
    console.log("age", age);
    console.log("id", id);

    const { name: personName } = obj;
    console.log("personName", personName);

# *let*

let statement declares a block-scoped local variable, optionally initializating it to a value

    function varTest() {
    var x = 1;
    {
        var x = 2;  // same variable!
        console.log(x);  // 2
    }
    console.log(x);  // 2
    }

    function letTest() {
    let x = 1;
    {
        let x = 2;  // different variable
        console.log(x);  // 2
    }
    console.log(x);  // 1
    }

# *Mocha*

Mocha 一种测试框架

*it* 是最小的测试单位，每个 *describe* 中可有多个 *it*

# *Promise*

Promise: 最终会变为可用值的代理，是一种处理异步代码方式（avoid callback hell）;

promise 最终会以被解决状态或者被拒绝状态结束。并在完成时调用相应的回调函数；

promise 设置

    let done = true
    // Create an instance using new Promise(function())
    // resovle, reject, and processing
    const isItDoneYet = new Promise((resolve, reject) => {
        if (done) {
            const workDone = "This is things created"
            resolve(workDone)
        } else {
            const why = "System busy"
            reject(why)
        }
    })

    // Another example: Promisfying
    const fs = require('fs')

    const getFile = (fileName) => {
        // Create a new Promise
        return new Promise((resolve, reject) => {
            fs.readFile(fileName, (err, data) => {
                if (err) {
                    reject(err)
                    return
                }
                resolve(data)
            })
        })
    }

    getFile('/etc/passwd')
        .then(data => console.log(data))
        .catch(err => console.error(err))
    
消费 promise

    const isItDoneYet = new Promise(/*... As mentioned above ...*/)
    const checkIfItsDone = () => {
        isItDoneYet
            // If resolved, run function in then{}
            .then(ok => {
                console.log(ok)
            })
            // If rejected, run function in catch{}
            .catch(err => {
                console.error(err)
            })
    }

链式 promise

    const status = response => {
        if (response.status >= 200 && response.status < 300) {
            return Promise.resolve(response)
        }
        return Promise.reject(new Error(response.statusText))
    }

    const json = response => response.json()

    fetch('/todos.json')
        // 检查响应状态
        // 若成功进入 .then
        // 若失败，进入 .catch
        .then(status)
        // .then(status)的resolve对象作为json的输入
        .then(json)
        // .then(json)的返回值
        .then(data => {
            console.log('Request succeed', data)
        })
        .catch(error => {
            console.log('Request failed', error)
        })

# *async function*

An async function is a function declared with the async keyword, and the await keyword is permitted within it.

The async and await keywords enable asynchronous, promise-based behavior to be written in a cleaner style,

The async function also means that the function will not affect the processing of other functions.

avoiding the need to explicitly condigure promise chains.

    function resolveAfter2Seconds() {
        return new Promise(resolve => {
            setTimeout(() => {
                resolve('resolved');
            }, 2000);
        });
    }

    async function asyncCall() {
        console.log('calling');
        const result = await resolveAfter2Seconds();
        console.log(result);
    }

    asyncCall();

Async functions always return a promise. If the return value of an async function is not explicitly a promise, it will be implicitly wrapped in a promise.

    async function foo() {
      return 1
    }

*similar to*

    function foo() {
        return Promise.resolve(1)
    }

## *node-js* 事件循环

    *nodejs* 代码是单线程的
    大多数浏览器中，每个浏览器选项卡都有一个事件循环，以使每个进程隔离开，并避免使用无限循环
    *JS* 中几乎所有的 *I/O* 基元都是非阻塞的。被阻塞时一个异常，所以 *JS* 中有很多回调
    调用堆栈、消息队列

## 变量

    全局变量
    局部变量
    块级作用域 let
    常量 const

## 关于 *await*

正确写法：

    async function main() { 
        for ( var i=234; i<244; i++) {
            const asset = seaport.api.getAsset({
                tokenAddress: "0xa5Bb28eecC6134F89745E34ec6aB5d5Bcb16dAD7",
                tokenId: i
            })

            asset.then((assetJs)=>{
                console.log(assetJs);
            }).catch((err) => {
                console.log(err);
            })
        }
    }

或者：

    async function main() { 
        for ( var i=234; i<244; i++) {
            const asset = await seaport.api.getAsset({
                tokenAddress: "0xa5Bb28eecC6134F89745E34ec6aB5d5Bcb16dAD7",
                tokenId: i
            })}
            console.log(asset);
    }

await 是 JS 中的语法糖，其底层机理和 function().then() 是一致的。

所以在一个 async 函数中两个先后的 await 之间是异步的。并不会：先执行第一个，执行完毕后再执行第二个。而是把两者作为额外的线程分开运行，并放在一个 FIFO 中。

## *nodejs* 中 *js* 文件互相引用

*fun1.js*

    var fun2 = require("./fun2");
    var fun3 = require("./fun3");
    function fun1(){
        console.log("I am fun1.");
        fun2.add(1, 2);
        fun3();
    }

    fun1();

*fun2.js*

    function reduce(a, b) {
        console.log("I am method reduce in fun2.");
        console.log(a-b);
    }
    function add(a, b) {
        console.log("I am method add in fun2.");
        console.log(a+b);
    }
    module.exports = {
        reduce,
        add
    }

*fun3.js*

    module.exports = function print() {
        console.log("I am a method in fun3.");
    }

