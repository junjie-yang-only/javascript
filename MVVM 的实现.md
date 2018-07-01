### 几种实现双向绑定的做法

- 发布者-订阅者模式
一般通过 sub，pub的方式实现数据和视图的绑定监听，更新数据方式通常做法是 vm.set('property', value)。

- 脏检查
angular.js 是通过脏值检测的方式对数据是否有变更，来决定是否更新视图。最简单的方式就是通过 setInterval() 定时轮询检测数据变动。只有在指定的事件触发的时候才进入脏值检测：
    - DOM 事件，比如用户输入文本，点击按钮(ng-click)
    - XHR 响应事件($HTTP)
    - 浏览器 location 变更事件($location)
    - Timer 事件($timeout，$interval)
    - 执行 $degist() 或 $apply()

- 数据劫持
vue.js 则是采用 数据劫持的方式来做数据绑定的，其中最核心的方法便是通过 Object.defineProperty() 来实现对属性的劫持，达到监听数据变动的目的。

实现 MVVM 的双向绑定，必须实现以下几点：

1. 实现一个数据监听器 Observer，能够对数据对象的所有属性进行监听，如有变动可拿到最新值并通知订阅者
2. 实现一个指定解析器 Compile，对每个元素节点的指令进行扫描和解析，根据指令模板替换数据，以及绑定相应的更新函数
3. 实现一个 Watcher，作为连接 Observer 和 Compile 的桥梁，能够订阅并收到每个属性的变动的通知，执行指令绑定的相应回调函数，从而更新视图
4. MVVM 入口函数，整合三者

#### 1. 实现Observer
需要 observer 的数据对象进行递归遍历，包括子属性对象的属性，加上 setter 和 getter，给这个对象的某个值赋值，就会触发 setter，就能监听数据变化。

```
let data = {name: 'king'}
observer(data)
data.name = 'queen'

function observer(data){
    if(!data || typeof data !== 'object'){
        return 
    }
    Object.keys(data).forEach(key => {
        defineReactive(data, key, data[key]);
    })
}

function defineReactive(data, key, val){
    observer(val);
    Object.defineProperty(data, key, {
        enumberable: true,
        configurable: false,
        get(){
            return val
        },
        set(newVal){
            console.log(`value changed ${newVal}`);
            val = newVal
        }
    })
}
```

接下来实现一个消息订阅器，维护一个数组，用来收集订阅者，数据变动触发 notify，再调用订阅者的 update 方法。进行代码改善：

```
function defineReactive(data, key, val) {
    let dep = new Dep();
    observer(val); // 监听子属性

    Object.defineProperty(data, key, {
        // ...
        set(newVal) {
            if(val === newVal) return;
            val = newVal;
            dep.notify();       // 通知所有订阅者
        }
    })
}

function Dep() {
    this.subs = []
}

Dep.prototype = {
    addSub(sub){
        this.subs.push(sub)
    },
    notity(){
        this.subs.forEach(sub => {
            sub.update()
        })
    }
}
```

想通过 dep 添加订阅者，就必须在闭包内操作,可以在 getter 里面操作

```
Object.defineProperty(data, key, {
    get(){
        // 由于需要在闭包内添加 watcher，所以通过 Dep 定义一个全局 target 属性，暂存 watcher，添加完后删除
        Dep.target && dep.addDep(Dep.target);
        return val
    }
    // ...
})

// Watcher.js
Watcher.prototype = {
    get(){
        Dep.target = this;
        this.value = data[key];     // 触发属性 getter，从而添加订阅者
        Dep.target = null
    }
}
```

#### 2. 实现Compile

compile 做的事情是解析模板指令，将模板中的变量替换成数据，然后初始化渲染页面视图，并将每一个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，更新视图。

因为遍历解析的过程有多次操作 DOM 节点，为提高性能和效率，会先将跟节点 el 转换成文档碎片 fragment 进行解析编译操作，解析完成，在将 fragment 添加回原来的真实 dom 节点。

```
function Compile(el) {
    this.$el = this.isElementNode(el) ? el : document.querySelector(el);
    if(this.$el) {
        this.$fragment = this.node2Fragment(this.$el);
        this.init();
        this.$el.qppendChild(this.$fragment)
    }
}
Compile.prototype = {
    init(){this.compileElement(this.$fragment)},
    node2Fragment(el){
        let fragment = document.createDocumentFragement(), child;
        while(child = el.firstChild) {
            fragment.appendChild(child);
        }
        return fragment;
    },
    compileElement(el){
        let childNodes = el.childNodes, me = this;
        [].slice.call(childNodes).forEach((node) => {
            let text = node.textContent;
            let reg = /\{\{(.*)\}\}/;   // 文本表达式
            // 按元素节点编译
            if(me.isElementNode(node)) {
                me.compile(node)
            }else if(me.isTextNode(node) && reg.test(text)){
                me.compileText(node, RegExp.$1);
            }
            // 遍历子节点
            if(node.childNodes && node.childNodes.length){
                me.compileElement(node)
            }
        })
    },
    compile(node){
        let nodeAttrs = node.attributes, me = this;
        [].slice.call(nodeAttrs).forEach(attr => {
            // 规定：指令以 v-xxx 命名
            // 如 <span v-text="content"></span> 中指令为 v-text
            let attrName = attr.name;
            if(me.isDirective(attrName)) {
                let exp = attr.value;
                let dir = attrName.substring(2);
                if (me.isEventDirective(dir)) {
                    // 事件指令, 如 v-on:click
                    compileUtil.eventHandler(node, me.$vm, exp, dir);
                } else {
                    // 普通指令
                    compileUtil[dir] && compileUtil[dir](node, me.$vm, exp);
                }
            }
        })
    }
}

