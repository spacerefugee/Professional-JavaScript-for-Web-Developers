### 第6章 面向对象的程序设计
- 面向对象(Object-Oriented, OO)的语言有一个标志，那就是它们都有类的概念，而通过类可以创建任意多个具有相同属性的方法和对象。

#### 6.1 理解对象
- 创建自定义对象的最简单方式就是创建一个Object的实例，然后再为它添加属性和方法。

##### 6.1.1 属性类型
- ECMAScript中有两种属性：数据属性和访问器属性。

###### 1.数据属性
- 数据属性包含一个数据值的位置。在这个位置可以读取和写入值。数据属性有4个描述其行为的特性。
  - [[Configurable]]：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为true。
  - [[Enumerable]]：表示能否通过for-in循环返回属性。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为true。
  - [[Writable]]：表示能否修改属性的值。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为true。
  - [[Value]]：包含这个属性的数据值。读取属性值的时候，从这个位置读；写入属性值的时候，把新值保存在这个位置。这个特性的默认值为undefined。
- 要修改属性默认的特性，必须使用ECMAScript5的Object.defineProperty())方法。这个方法接收三个参数：属性所在的对象、属性的名字和一个描述符对象。其中，描述符(descriptor)对象的属性必须是：configurable、enumerable、writable和value。设置其中的一或多个值，可以修改对应的特性值。

###### 2. 访问器属性
- 访问器属性不包含数据值；它们包含一对getter和setter函数（不过这两个函数都不是必须的）。在读取访问器属性时，会调用getter函数，这个函数负责返回有效的值；在写入访问器属性时，会调用setter函数并传入新值，这个函数负责决定如何处理数据。访问器属性有如下4个特性。
  - [[Configurable]]：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性。对于直接在对象上定义的属性，这个特性的默认值为true。
  - [[Enumerable]]：表示能否通过for-in循环返回属性。对于直接在对象上定义的属性，这个特性的默认值为true。
  - [[Get]]：在读取属性时调用的函数。默认值为undefined。
  - [[Set]]：在写入属性时调用的函数。默认值为undefined。
- 访问器属性不能直接定义，必须使用Object.defineProperty()来定义。

##### 6.1.2 定义多个属性
- 由于为对象定义多个属性的可能性很大，ECMAScript5又定义了一个Object.defineProperties()方法。利用这个方法可以通过描述符一次定义多个属性。这个方法接收两个对象参数：第一个对象时要添加和修改其属性的对象，第二个对象的属性与第一个对象中要添加或修改的属性一一对应。

##### 6.1.3 读取属性的特性
- 使用ECMAScript5的Object.getOwnPropertyDescriptor()方法，可以取得给定属性的描述符。这个方法接收两个参数：属性所在的对象和要读取其描述符的属性名称。返回值是一个对象，如果是访问器属性，这个对象的属性有configurable、enumerable、get和set；如果是数据属性，这个对象的属性有configurable、enumerable、writable和value。

#### 6.2 创建对象
- 虽然Object构造数或对象字面量都可以用来创建单个对象，但些方式有个明显的缺点：使用同一个接口创建很多对象，会产生大量的重复代码。为解决这个问题，人们开始使用工厂模式的一种变体。

##### 6.2.1 工厂模式
- 工厂模式是软件工程领域一种广为人知的设计模式，这种模式抽象了创建具体对象的过程。

```javascript
function createPerson(name, age, job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        alert(this.name);
    };
    return o;
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```

- 工厂模式虽然解决了创建多个相似对象的问题，但却没有解决对象识别的问题（即怎样知道一个对象的类型）。

##### 6.2.2 构造函数模式
- ECMAScript中的构造函数可以用来创建特定类型的对象。

```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function() {
        alert(this.name);
    };
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```

- 与工厂模式的代码有以下不同之处：
  - 没有显式地创建对象；
  - 直接将属性和方法赋给了this对象；
  - 没有return语句。
- 此外函数名Person使用的是大写字母P。按照管理，构造函数始终都应该以一个大写字母开头，而非狗仔函数则应该以一个小写字母开头。
- 如上述例子所示，两个Person对象都有一个constructor（构造函数）属性，该属性指向Person。
- 可以使用instanceof检测对象类型。
- 创建自定义的构造函数意味着将来可以将它的实例标识为一种特定的类型；而这正是构造函数模式胜过工厂模式的地方。

###### 1. 将构造函数当做函数
- 构造函数与其他函数的唯一区别，就在于调用它们的方式不同。不过，构造函数毕竟也是函数，不存在定义构造函数的特殊语法。任何函数，只要通过new操作符来调用，那它就可以作为构造函数；而任何函数，如果不通过new操作符来调用，那它跟普通函数也不会有什么两样。例如，前面例子中定义的Person()函数可以通过下列任何一种方式来调用。

```javascript
// 当构造函数使用
var person = new Person("Nicholas", 29, "Software Engineer");
person.sayName; // "Nicholas"
// 作为普通函数调用
Person("Greg", 27, "Doctor"); // 添加到Window
window.sayName(); // "Greg"
// 在另一个对象的作用域中调用
var o = new Object();
Person.call{o, "Kristen", 25, "Nurse"};
o.sayName(); // "Kristen"
```

###### 2. 构造函数的问题
- 使用构造函数的主要问题，就是每个方法都要在每个实例上重新创建一遍。
- 创建两个完成同样任务的Function实例确实没有必要；况且有this对象在，根本不用在执行代码前就把函数绑定到特定对象上面。因此可以像下面这样，通过把函数定义转移到构造函数外部解决这个问题。

```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = sayName;
}
function sayName() {
    alert(this.name);
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```

- 在构造函数外部定义全局函数，这样确实解决了两个函数做同一件事的问题，可是心的问题由来了：在全局作用域中定义的函数实际上只能被某个对象调用，这让全局作用域有点名不副实。而更让人无法接受的是：如果对象需要定义很多方法，那么就要定义很多歌全局函数，于是我们这个自定义的引用类型就四号没有封装性可言了。这些问题可以通过使用原型模式来解决。

##### 6.2.3 原型模式
- 我们创建的每个函数都有一个prototype（原型）属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。如果按照字面意思来理解，那么prototype就是通过调用构造函数而创建的那个对象实例的原对象。使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法。换句话说，不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中，如下面的例子所示。

```javascript
function Person() {
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function() {
    alert(this.name);
};
var person1 = new Person();
person1.sayName(); // "Nicholas"
var person2 = new Person();
person2.sayName(); // "Nicholas"
alert(person1.sayName == person2.sayName); // true
```
- person1和person2访问的都是同一组属性和同一个sayName()函数。

###### 1. 理解原型对象
- 无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个prototype属性，这个属性指向函数的原型对象。在默认情况下，所有原型对象都会自动获得一个constructor（构造函数）属性，这个属性包含一个指向prototype属性所在函数的指针。就拿前面的例子来说，Person.prototype.constructor指向Person。而通过这个构造函数我们还可继续为原型对象添加其他属性和方法。
- 可以通过isPrototypeOf()方法来确定对象之间是否存在原型与实例之间的关系。如果[[Prototype]]指向调用isPrototypeOf()方法的对象，那么这个方法就返回true。
- 使用hasOwnProperty()方法可以检测一个属性是存在于实例中，还是存在于原型中。这个方法只在给定属性存在于对象实例中，才会返回true。或者说，只有当实例重写了原型中的属性才会返回true。

###### 2. 原型与in操作符
- 有两种方式使用in操作符：单独使用和在for-in循环中使用。
- 在单独使用时，in操作符会在通过对象能够访问给定属性时返回true，无论该属性存在于实例中还是原型中。
- 同时使用hasOwnProperty()方法和in操作符，可以确定该属性到底是存在于对象中还是存在于原型中。如下所示

```javascript
function hasPrototpyeProperty(object, name) {
    return !object.hasOwnProperty(name) && (name in object);
}
```
- 由于in操作符只要通过对象能够访问到属性就返回true，hasOwnProperty()只在属性存在于实例中才返回true，因此只要in操作符返回true而hasOwnProperty()返回false，就可以确定属性是原型中的属性。
- 在使用for-in循环时，返回的是所有能够通过对象访问的、可枚举的（enumerated）属性，其中既包括存在于实例中的属性，也包括存在于原型中的属性。屏蔽了原型中不可枚举属性（即将[[Enumerable]]标记的属性）的实例属性也会在for-in循环中返回，因为根据规定，所有开发人员定义的属性都是可枚举的。
- 使用Object.keys()方法可以接收一个对象作为参数，返回一个包含所有可枚举属性的字符串数组。
- 使用Object.getOwnPropertyNames()可以得到所有实例属性，无论它是否可枚举。

###### 3. 更简单的原型语法
- 为减少不必要的输入，也为了从视觉上更好地封装原型的功能，更常见的做法是用一个包含所有属性的方法的对象字面量来重写整个原型对象，如下面的例子所示：

```javascript
function Person() {}
Person.prototype = {
    name : "Nicholas",
    age : 29,
    job : "Software Engineer",
    sayName : function() {
        alert(this.name);
    }
};
```
- 在上面的代码中，constructor属相不再指向Person，如果constructor的值真的很重要，可以像下面这样特意将它设置回适当的值。

```javascript
function Person() {}
Person.prototype = {
    constructor : Person,
    name : "Nicholas",
    age : 29,
    job : "Software Engineer",
    sayName : function() {
        alert(this.name);
    }
};
```
- 这种方式重设constructor属性会导致它的[[Enumerable]]特性被设置为true。默认情况下，原生的constructor属性是不可枚举的，因此如果想兼容ECMAScript5的JavaScript引擎，可以用Object.defineProperty()。

```javascript
// 重设构造函数，只适用于ECMAScript5兼容的浏览器
Object.defineProperty(Person.prototype, "constructor", {
    enumerable : false,
    value : Person
});
```

###### 4. 原型的动态性
- 由于在原型中查找值的过程是一次搜索，因此我们队原型对象所做的任何修改都能够立即从实例上反映出来——即使是先创建了实例后修改原型也照样如此。
- 如果重写整个原型对象情况就不一样了。调用构造函数时会为实例添加一个纸箱最初原型的[[Prototype]]指针，而把原型修改为另一个对象就等于切断了构造函数与最初原型之间的联系。

###### 5. 原生对象的原型
- 通过原生对象的原型，不仅可以取得所有默认方法的引用，而且也可以定义新方法。可以像改自定义对象的原型一样修改原生对象的原型，因此可以随时添加方法。
- 尽管可以这样做，但是不推荐在产品化的程序中修改原生对象的原型。如果因某个实现中缺少某个方去，就在原生对象的原型中添加这个方法，那么当在另一个持该方法的实现中运行代码时，就可能会导致命名冲突。而且，这样做也可能会意外地重写原生方法。

###### 6. 原型对象的问题
- 因为省略了为构造函数传递初始化参数这一环节，结果所有实例在默认情况下都将取得相同的属性值。虽然这会在某种程度上带来一些不方便，但还不是原型的最大问题。
- 原型中所有属性是被很多实例共享的，这种共享对于函数非常合适。对于那些包含基本值的属性也说得过去，毕竟通过在实例上添加一个同名属性，可以隐藏原型中的对应属性。然而，对于包含引用类型值的属性来说，问题就比较突出了。修改引用类型的属性之后，所有实例中的属性都可以共享，如果实例要有属于自己的全部属性的话，这就不合适了。

##### 6.2.4 组合使用构造函数模式和原型模式
- 创建自定义类的最常见方式，就是组合使用构造函数模式与原型模式。构造函数模式用于实例属性，而原型模式用于定义方法和共享的属性。结果，每个实例都会有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存。另外，这种混成模式还支持向构造数传递参数；可谓是集两种模式之长。下面的代码重写了前面的例子。

```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.freinds = ["Shelby", "Court"];
}
Person.prototype = {
    constructor : Person,
    sayName : function() {
        alert(this.name);
    }
}
```
- 这种构造函数与原型混成的模式，是目前在ECMAScript中使用最广泛、认同度最高的一种创建自定义类型的方法。可以说，这是用来定义引用类型的一种默认模式。

##### 6.2.5 动态原型模式
动态原型模式把所有信息都封装在了构造函数中，而通过在构造函数中初始化原型（仅在必要的情况下），又保持了同事使用构造函数和原型的优点。

```javascript
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.freinds = ["Shelby", "Court"];
    if (typeof this.sayName != "function") {
        Person.prototype.sayName = function() {
            alert(this.name);
        };
    }
}
```
- 使用动态原型模式时，不能使用对象字面量重写原型。如果在已经创建了实例的情况下重写原型，那么久会切断现有实例与新原型之间的联系。

##### 6.2.6 寄生构造函数模式
- 寄生(parasitic)构造函数模式的基本思想是创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回新创建的对象；但从表面上看，这个函数又很像是典型的构造函数。下面是例子：

```javascript
function Person(name, age, job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        alert(this.name);
    };
    return o;
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName(); // "Nicholas"
```
- 除了使用new操作符并把使用的包装函数叫做构造函数之外，这个模式跟工厂模式其实是一模一样的。这个模式可以在特殊的情况下用来为对象创建构造函数。
- 关于寄生构造函数模式，有一点需要说明：首先，返回的对象与构造函数或者与构造床数的原型属性之间没有关系；也就是说，构造函数返回的对象与在构造函数外部创建的对象没有什么不同。为此，不能依赖instanceof操作符来确定对象类型。由于存在上述问题，建议在可以使用其他模式的情况下，不要使用这种模式。

##### 6.2.7 稳妥构造函数模式
- 稳妥对象(durable objects)指的是没有公共属性，而且其方法也不引用this对象。稳妥对象最适合在一些安全的环境中（这些环境中会禁止使用this和new），或者在防止数据被其他应用程序（如Mashup程序）改动时使用。
- 稳妥构造函数遵循与寄生构造函数类似的模式，但有两点不同：一是新创建对象的实例方法不引用this；二是不使用new操作符调用构造函数。按照稳妥构造函数的要求，可以将前面的Person构造函数重写如下。

```javascript
function Person(name, age, job) {
    // 创建要返回的对象
    var o = new Object();
    // 可以在这里定义私有变量和函数
    
    // 添加方法
    o.sayName = function() {
        alert(name);
    };
    // 返回对象
    return o;
}
var friend = Person("Nicholas", 29, "Software Engineer");
friend.sayName(); // "Nicholas"
```
- 在这种模式创建的对象中，除了使用sayName()方法之外，没有其他办法访问name的值。稳妥构造函数模式提供的这种安全性，使得它非常适合在某些安全执行环境——例如，ADsafe和Caja提供的环境——下使用。
- 与寄生构造函数模式类似，使用稳妥构造函数模式创建的对象与构造函数之间也没有什么关系，因此instanceof操作符对这种对象也没有意义。

#### 6.3 继承
- ECMAScript中无法时间接口继承，只支持实现继承，而且其实实现继承主要依靠原型链来实现的。

##### 6.3.1 原型链
- 实现原型链有一种基本模式，其代码大致如下。

```javascript
function SuperType() {
    this.property = true;
}
SuperType.prototype.getSuperValue = function() {
    return this.property;
};
function SubType() {
    this.subproperty = false;
}

// 继承了SuperType
SubType.prototype = new SuperType();

SubType.prototype.getSubValue = function() {
    return this.subproperty;
};

var instance = new SubType();
alert(instance.getSuperValue()); // true
```
- 以上代码定义了两个类型：SuperType和SubType。每个类型分别有一个属性和一个方法。它们的主要区别是SubType继承了SuperType，而继承是通过创建SuperType的实例，并将该实例赋给SubType.prototype实现的。实现的本质是重写原型对象，代之以一个新类型的实例。

###### 1. 别忘记默认的原型
- 所有引用类型默认都继承了Object，而这个继承也是通过原型链实现的。所有函数的默认原型都是Object的实例，这也是所有自定义类型都会继承toString()、valueOf()等默认方法的原因。

###### 2. 确定原型和实例的关系
- 可以通过两种方式来确定原型和实例之间的关系。
- 第一种方式是使用instanceof操作符，只要用这个操作符来测试实例与原型链中出现过的构造函数，结果就会返回ture。
- 第二种凡是是使用isPrototypeOf()方法，同样只要是原型链中出现过的原型，都可以说说是该原型链所派生的实例的原型，因此isPrototypeOf()方法也会返回true。

###### 3. 谨慎地定义方法
- 子类型有时候需要重写超类型中的某个方法，或者需要添加超类型中不存在的某个方法。但不管怎样，给原型添加方法的代码一定要放在替换原型的语句之后。
- 在通过原型链实现继承时，不能使用对象字面量穿件原型方法。因为这样做就会重写原型链。

###### 4. 原型链的问题
- 在通过原型来实现继承时，原型实际上回变成另一个类型的实例。于是，原先的实例属性野牛顺理成章地变成了现在的原型属性了。
- 在创建子类型的实例时，不能向超类型的构造函数中传递参数。
- 因为以上问题，实践中很少会单独使用原型链。

##### 6.3.2 借用构造函数
- 借用构造函数(constructor stealing)（有时候也叫做伪造对象或经典继承），这种技术在子类型构造函数的内部调用超类型构造函数。通过apply()和call()方法可以在（将来）新创建的对象上执行构造函数。如下所示：

```javascript
function SuperType() {
    this.colors = ["red", "blue", "green"];
}
function SubType() {
    // 继承了SuperType
    SuperType.call(this);
}
var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors); // "red,blue,green,black"

var instance2= new SubType();
alert(instance2.colors); // "red,blue,green"
```

###### 1. 传递参数
- 对于原型链而言，借用构造函数有一个很大的优势，即可以在子类型构造函数中向超累型构造函数传递参数。如下例子：

```javascript
function SuperType(name) {
    this.name = name;
}
function SubType() {
    // 继承SuperType，同事还传递了参数
    SuperType.call(this, "Nicholas");
    
    // 实例属性
    this.age = 29;
}

var instance = new SubType();
alert(instance.name); // "Nicholas"
alert(instance.age); // 29
```

###### 2. 借用构造函数的问题
- 方法都在构造函数中定义，因此函数复用就无从谈起。而且，在超类型的原型中定义的方法，对子类型而言也是不可见的，结果所有类型都只能使用构造函数模式。
- 因为以上问题，借用构造函数的技术也是很少单独使用的。

##### 6.3.3 组合继承
- 组合继承(combination inheritance)，有时候也叫做伪经典继承，指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。其背后的思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。

```javascript
function SuperType(name) {
    this.name = name;
    this.colors = [{"red", "blue", "green"}];
}
SuperType.prototype.sayName = function() {
    alert(this.name);
};
function SubType(name, age) {
    // 继承属性
    SuperType.call(this.name);
    this.age = age;
}
// 继承方法
SubType.prototype = new SuperType();
SubType.prototype.sayAge = function() {
    alert(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors); // "red,blue,green,black"
instance1.sayName(); // "Nicholas"
instance1.sayAge(); // 29

var instance2 = new SubType("Greg", 27);
alert(instance2.colors); // "red,blue,green"
instance2.sayName(); // "Greg"
instance2.sayAge(); // 27
```
- 组合继承避免了原型链和借用构造函数的缺陷，融合了它们的有点，成为JavaScript中最常用的继承模式。而且，instanceof和isPrototypeOf()也能够用于识别基于组合继承创建的对象。

##### 6.3.4 原型式继承
- 原型式继承这种方法并没有使用严格意义上的构造函数。
- ECMAScript5通过新增Object.create()方法规范化了原型式继承。这个方法接收两个参数：一个用作新对象原型的对象和（可选的）一个为新对象定义额外属性的对象。

```javascript
var person = {
    name : "Nicholas",
    friends : ["Shelby", "Court", "Van"]
};

var anotherPerson = Object.create(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");

var yetAnotherPerson = Object.create(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");

alert(person.friends); // "Shelby,Court,Van,Rob,Barbie"
```
- 在没有必要兴师动众地创建构造函数，而指向让一个对象与另一个对象保持类似的情况下，原型式继承是完全可以胜任的。不过别忘了，包含引用类型值的属性始终都会共享相应的值，就像使用原型模式一样。

##### 6.3.5 寄生式继承
- 寄生式(parastic)继承的思路与寄生构造函数和工厂模式类似，即创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真的是他做了所有工作一样返回对象。

```javascript
function createAnother(original) {
    var clone = object(original); // 通过调用函数创建一个新对象
    clone.sayHi = function() { // 以某种方式来增强这个对象
        alert("Hi!");
    };
    return clone; // 返回这个对象
}
var person = {
    name : "Nicholas",
    friends : ["Shelby", "Court", "Van"]
}
var anotherPerson = createAnother(person);
anotherPerson.sayHi(); // "Hi!"
```
- 在主要考虑对象而不是自定义类型和构造函数的情况下，寄生式继承也是一种有用的模式。前面示范继承模式时使用的object()函数不是必须的，任何能够返回新对象的函数都适用于此模式。
- 使用寄生式继承来为对象添加函数，会由于不能做到函数复用而降低效率，这一点与构造函数模式类似。

##### 6.3.6 寄生组合式继承
- 所谓寄生组合式继承，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。其背后的基本思路是：不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非就是超类型的原型的一个副本而已。本质上，就是使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型。

```javascript
function inheritPrototype(subType, superType) {
    var prototype = object(superType.prototype); // 创建对象
    prototype.consructor = subType; // 增强对象
    subType.prototype = prototype; // 指定对象
}
```
- 这个示例中的inheritPrototype()函数实现了寄生组合式继承的最简单形式。这个函数接收两个参数：子类型构造函数和超类型构造函数。在函数内部，第一步是创建超类型的一个副本。第二步是为创建的副本添加constructor属性，从而弥补因重写原型而失去的默认的constructor属性。最后一步，将新创建的对象（即副本）赋值给子类型的原型。
