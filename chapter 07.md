### 第7章 函数表达式
- 定义函数的方式有两种：一种是函数声明，另一种就是函数表达式。

```javascript
// 函数声明
function functionName(arg0, arg1, arg2) {
    // 函数体
}
```
- 关于函数声明，它的一个重要特征就是函数声明提升(function declaration hoisting)，意思是在执行代码之前会先读取函数声明，这就意味着可以把函数声明放在调用它的语句后面。

```javascript
// 函数表达式
function functionName = function(arg0, arg1, arg2) {
    // 函数体
};
```
- 函数表达式与其他表达式一样，在使用前必须先赋值。

#### 7.1 递归
- 递归函数是在一个函数通过名字调用自身的情况下构成的。如下所示。

```javascript
function factorial(num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * arguments.callee(num-1);
    }
}
```
- arguments.callee是一个纸箱正在执行的函数的指针，因此可以用它来实现对函数的递归调用。
- 但是在严格模式下，不能通过脚本访问arguments.callee，访问这个属性会导致错误。不过可以使用命名函数表达式来达成仙童的结果。例如：

```javascript
var factorial = (function f(num) {
    if (num <= 1) {
        return 1;
    } else {
        return num * f(num-1);
    }
});
```
- 以上代码创建了一个名为f()的命名函数表达式，然后将它赋值给变量factorial.即便把函数赋值给了另一个变量，函数的名字f仍然有效。所以递归调用照样能正确完成。这种方式在严格模式和非严格模式下都行得通。

#### 7.2 闭包
- 闭包是指有权访问另一个函数作用域中的变量的函数。创建闭包的常见方式，就是在一个函数内部创建另一个函数。
- 由于闭包会携带包含它的函数的作用域，因此会比其他函数占用更多的内存。过度使用闭包可能会等致内存占用过多，建议只在绝对必要时再考虑使用闭包。虽然像V8等优化后的JavaScript引擎会尝试回收被团包占用的内存，但还是要慎重使用闭包。

##### 7.2.1 闭包与变量
- 闭包只能取得包含函数中任何变量的最后一个值。

##### 7.2.2 关于this对象
- this对象是在运行时基于函数的执行环境绑定的：在全局函数中，this等于window，而当函数被作为某个对象的方法调用时，this等于那个对象。不过，匿名函数的执行环境具有全局性，因此其this对象通常指向window。但有时候由于编写闭包的方式不同，这一点可能不会那么明显。

##### 7.2.3 内存泄漏
- 如果闭包的作用域链中保存着一个HTML元素，那么久意味着该元素将无法被销毁。

#### 7.3 模仿块级作用域
- JavaScript没有块级作用域的概念，这意味着在块语句中定义的变量，实际上是包含在函数中而非语句中创建的。
- 匿名函数可以用来模仿块级作用域，如下所示：

```javascript
(function () {
    // 这里是块级作用域
})();
```
- 以上代码定义并立即调用了一个匿名函数。将函数声明包含在一对圆括号中，表示它实际上市一个函数表达式。而紧随其后的另一对圆括号会立即调用这个函数。

#### 7.4 私有变量
- 严格来讲，JavaScript中没有私有成员的概念所有对象属性都是公有的。但是有一个私有变量的概念。任何在函数中定义的变量，都可以认为是私有变量，因为不能再函数的外部访问这些变量。私有变量包括函数的参数、局部变量和在函数内部定义的其他函数。
- 我们把有权访问私有变量和私有函数的公有方法称为特权方法(privileged method)。有两种在对象上创建特权方法的方式。第一种是在构造函数中定义特权方法，基本模式如下。

```javascript
function MyObject() {
    // 私有变量和私有函数
    var privateVaribale = 10;
    function privateFunction() {
        return false;
    }
    // 特权方法
    this.publicMethod = function() {
        privateVaribale++;
        return privateFunction();
    };
}
```
- 这个模式在构造函数内部定义了所有私有变量和函数，然后又继续创建了能够访问这些私有成员的特权方法。
- 利用私有和特权成员，可以隐藏那些不应该被直接修改的数据。例如。

```javascript
function Person(name) {
    this.getName = function() {
    return name;
    };
    this.setName = function(value) {
        name = value;
    };
}
var person = new Person("Nicholas");
alert(person.getName()); // "Nicholas"
person.setName("Greg");
alert(person.getName()); // "Greg"
```

##### 7.4.1 静态私有变量
- 通过在私有作用域中定义私有变量或函数，同样也可以创建特权方法，其基本模式如下所示。

```javascript
(function() {
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction() {
        return false;
    }
    // 构造函数
    MyObject = function () {
        
    };
    // 公有/特权方法
    MyObject.prototype.publicMethod = function() {
        privateVariable++;
        return privateFunction();
    };
})();
```
- 这个模式创建了一个私有作用域，并在其中封装了一个构造函数及相应的方法。在私有作用域中，首先定义了私有变量和私有函数，然后又定义了构造函数及其公有方法。公有方法是在原型上定义的，这一点体现了典型的原型模式。需要注意的是．这个模式在定义构造函数时并没有使用数声明，而是使用了函数表达式。函数声明只能创建局部数，但那并不是我们想要的。出于同样的原因，我们也没有在声明MyObject时使用var关键字。记住：初始化未经声明的变量，总是会创建一个全局变量。因此，MyObject就成了一个全局变量，能够在私有作用域之外被访问到。但也要知道。在严格模式下给未经声明的变量赋值会导致错误。
- 这个模式与在构造函数中定义特权方法的主要区别，就是在于私有变量和函数是由实例共享的。由于特权方法是在原型上定义的，因此所有实例都使用同一个函数。而这个特权方法，作为一个闭包，总是保存着对包含作用域的引用。

##### 7.4.2 模块模式
- 模块模式通过为单利添加私有变量和特权方法能够使其得到增强。

```javascript
var singleton = function() {
    // 私有变量和私有函数
    var privateVariable = 10;
    function privateFunction() {
        return false;
    }
    // 特权/公有方法和属性
    return {
        publicProperty : true,
        publicMethod : function() {
            privateVariable++;
            return privateFunction();
        }
    };
}();
```

##### 7.4.3 增强的模块模式
- 有人进一步改进了模块模式，即在返回对象之前加入对其增强的代码。这种增强的模块模式适合那些单利必须是某种类型的实例，同事还必须添加某些属性和（或）方法对其加以增强的情况。
