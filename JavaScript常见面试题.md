#### 1. instanceof
用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性

语法：
```
object instanceof constructor
```

object: 要检测的对象
constructor: 某个构造函数

instanceof 用来检测 constructor.prototype 是否存在于参数 object 的原型链上。

```
// 定义构造函数
function C(){} 
function D(){} 

var o = new C();


o instanceof C; // true，因为 Object.getPrototypeOf(o) === C.prototype


o instanceof D; // false，因为 D.prototype不在o的原型链上

o instanceof Object; // true,因为Object.prototype.isPrototypeOf(o)返回true
C.prototype instanceof Object // true,同上

C.prototype = {};
var o2 = new C();

o2 instanceof C; // true

o instanceof C; // false,C.prototype指向了一个空对象,这个空对象不在o的原型链上.

D.prototype = new C(); // 继承
var o3 = new D();
o3 instanceof D; // true
o3 instanceof C; // true 因为C.prototype现在在o3的原型链上
```

可以使用 **Array.isArray(myObj)** 或者 **Object.prototype.toString.call(myObj) === "[object Array]"** 来检测传过来的对象是否是一个数组。

#### 2. 下面的代码会在 console 输出神马？为什么？

```javascirpt
(function(){
    var a = b = 3
})()
console.log(a) // undifined
console.log(b) // 3
```

变量作用域相关

拆解成：
```
b = 3;
var a = b;
```

b 成了全局变量，a 是自执行函数的一个局部变量

```
var myObject = {
    foo: "bar",
    func: function() {
        var self = this;
        console.log("outer func:  this.foo = " + this.foo);     // bar
        console.log("outer func:  self.foo = " + self.foo);     // undefined
        (function() {
            console.log("inner func:  this.foo = " + this.foo); // bar
            console.log("inner func:  self.foo = " + self.foo); // bar
        }());
    }
};
myObject.func();
```


#### 3. 为什么要用立即执行函数表达式
1. 类似于在循环中定时输出数据项
2. 类似于 jquery/node 的插件和模块开发；避免变量污染

```
for(var i = 0; i < 5; i++){
    (function(i){
        setTimeout(() => {
            console.log(i)
        }, 1000)
    })(i)
}
```

#### 4. JavaScript 解析器是否自动填充分号

```
function foo1()
{
  return {
      bar: "hello"
  };
}

function foo2()
{
  return
  {
      bar: "hello"
  };
}

a = foo1();     // {bar: "hello"}
b = foo2();     // undefined
```

```
var test = 1 + 
2
console.log(test);  //3
```

为了正确的解析代码，就不会自动填充分号，但是对于 **return**、**break**、**continue** 等语句，如果后面紧跟换行，解析器一定会在后面填充分号(;)。

#### 5. NaN

NaN 是 Not a Number 的缩写，JavaScript 的一种特殊数值，其类型是 Number，可以通过 isNaN(parm) 来判断一个值是否是 NaN :

```
console.log(isNaN(NaN)); //true
console.log(isNaN(23)); //false
console.log(isNaN('ds')); //true
console.log(isNaN('32131sdasd')); //true
console.log(NaN === NaN); //false
console.log(NaN === undefined); //false
console.log(undefined === undefined); //false
console.log(typeof NaN); //number
console.log(Object.prototype.toString.call(NaN)); //[object Number]
```

ES6 中，isNaN() 成为了 Number 的静态方法：Number.isNaN()

#### 6. JavaScript 中的二进制

```
console.log(0.1 + 0.2);   //0.30000000000000004
console.log(0.1 + 0.2 == 0.3);  //false
```

由于采用二进制，JavaScript 也不能有限表示 1/10、1/2 等这样的分数。在二进制中，1/10(0.1)被表示为 0.00110011001100110011…… 注意 0011 是无限重复的，这是舍入误差造成的，所以对于 0.1 + 0.2 这样的运算，操作数会先被转成二进制，然后再计算：

```
0.1 => 0.0001 1001 1001 1001…（无限循环）
0.2 => 0.0011 0011 0011 0011…（无限循环）
```

双精度浮点数的小数部分最多支持 52 位，所以两者相加之后得到这么一串 0.0100110011001100110011001100110011001100...因浮点数小数位的限制而截断的二进制数字，这时候，再把它转换为十进制，就成了 0.30000000000000004。

对于保证浮点数计算的正确性，有两种常见方式。

一是先升幂再降幂

```
function add(num1, num2){
  let r1, r2, m;
  r1 = (''+num1).split('.')[1].length;
  r2 = (''+num2).split('.')[1].length;

  m = Math.pow(10,Math.max(r1,r2));
  return (num1 * m + num2 * m) / m;
}
console.log(add(0.1,0.2));   //0.3
console.log(add(0.15,0.2256)); //0.3756
```

二是使用内置的 toPrecision() 和 toFixed() 方法，注意，方法的返回值字符串。

```
function add(x, y) {
    return x.toPrecision() + y.toPrecision()
}
console.log(add(0.1,0.2));  //"0.10.2"
```

#### 7. 判断 x 是否是一个整数

可以将 x 转换成 10 进制，判断和本身是不是相等

```
function isInteger(x){
    return parseInt(x, 10) === x
}

parseInt(10, 8)     // 8
parseInt(30, 16)    // 48
```

ES6 对数值进行了扩展，提供了静态方法 isInteger() 来判断参数是否是整数：

```
Number.isInteger(25) // true
Number.isInteger(25.0) // true
Number.isInteger(25.1) // false
Number.isInteger("15") // false
Number.isInteger(true) // false
```

JavaScript能够准确表示的整数范围在 -2^53 到 2^53 之间（不含两个端点），超过这个范围，无法精确表示这个值。ES6 引入了Number.MAX_SAFE_INTEGER 和 Number.MIN_SAFE_INTEGER这两个常量，用来表示这个范围的上下限，并提供了 Number.isSafeInteger() 来判断整数是否是安全型整数。

#### 8. 判断字符串是不是回文数

```
function isPalindrome(str){
    str = str.replace(/\W/, '').toLowerCase()
    return (str === str.split('').reverse().join(''))
}
```

#### 9. 运算符的输出

```
console.log(1 && 2 && 0);  //0
console.log(1 && 0 && 1);  //0
console.log(1 && 2 && 3);  //3

console.log(1 || 2 || 0); //1
console.log(0 || 2 || 1); //2
console.log(0 || 0 || false); //false
```

如果逻辑与和逻辑或做混合运算，则逻辑与的优先级高：

```
console.log(1 && 2 || 0); //2
console.log(0 || 2 && 1); //1
console.log(0 && 2 || 1); //1
```

空数组([])和空对象({}):

```
console.log([] == false) //true
console.log({} == false) //false
console.log(Boolean([])) //true
console.log(Boolean({})) //true
```

#### 10. 对象的属性值

```
var a = {},
    b = {key:'b'},
    c = {key:'c'};

a[b] = 123;
a[c] = 456;

console.log(a[b]);  // 456
```

解释如下：

> The reason for this is as follows: When setting an object property, JavaScript will implicitly stringify the parameter value. In this case, since b and c are both objects, they will both be converted to "[object Object]". As a result, a[b] anda[c] are both equivalent to a["[object Object]"] and can be used interchangeably. Therefore, setting or referencing a[c] is precisely the same as setting or referencing a[b].

