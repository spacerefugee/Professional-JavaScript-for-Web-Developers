### 第8章 BOM

#### 8.1 window对象
- BOM的核心对象时window，它表示浏览器的一个实例。在浏览器中，window对象有双重角色，它既是通过JavaScript访问浏览器窗口的一个借口，又是ECMAScript规定的Global对象。这一位置在网页中定义的任何一个对象、变量和函数，都以window作为其Global对象，因此有权访问parseInt()等方法。

##### 8.1.1 全局作用域
- 由于window对象同事扮演着ECMAScript中Global对象的角色，因此所有在全局作用域中声明的变量、函数都会变成window对象的属性和方法。
- 定义全局变量与在window对象上直接定义属性还是有一点差别：全局变量不能通过delete操作符删除，而直接在window对象上定义的属性可以。例如：

```javascript
var age = 29;
window.color = "red";
// 在IE<9时抛出错误，在其他所有浏览器中都返回false
delete window.age;
// 在IE<9时抛出错误，在其他所有浏览器中都返回true
delete window.color;

alert(window.age); // 29
alert(window.color); // undefined
```
- 使用var语句添加的window属性有一个名为[[Configurable]]的特性，这个特性的值被设置为false，因此这样定义的属性不可以通过delete操作符删除。
- 尝试访问未声明的变量会抛出错误，但是通过查询window对象，可以知道某个可能未声明的变量是否存在。例如：

```javascript
// 这里会抛出错误，因为oldValue未定义
var newValue = oldValue;
// 这里不会抛出错误，因为这是一次属性查询
// newValue的值是undefined
var newValue = window.oldValue;
```

##### 8.1.2 窗口关系及框架
- 如果页面中包含框架，则每个框架都拥有自己的window对象，并且保存在frames集合中。在frames集合中，可以通过数值索引（从0开始，从左至右，从上到下〕或者框架名称来访问相应的window对象。每个window对象都有一个name属性，其中包含框架的名称。
- top对象始终指向最高（最外）层的框架，也就是浏览器窗口。使用它可以确保在一个框架中正确地访问另一个框架。因为对于在一个框架中编写的任何代码来说，其中的window对象指向的都是那个框架的特定实例，而非最高层的框架。
- 与top相对的另一个window对象时parent。顾名思义，parent（父）对象始终指向当前框架的直接上层框架。在某些情况下，parent有可能等于top；但在没有框架的情况下，parent一定等于top（此时它们都等于window）。
- 除非最高层窗口是通过window.open()打开的，否则window对象的name属性不会包含任何值。
- 与框架有关的最后一个对象时self，它始终指向window；实际上，self和window对象可以互换使用。引入self对象的目的是为了与top和parent对象对应起来，因此它不格外包含其他值。
- 所有这些对象都是window对象的属性，可以通过window.parent、window.top等形式来访问。同时，也意味着可以将不同层次的window对象连缀起来，例如window.parent.parent.frames[0]。

##### 8.1.3 窗口位置
- 用来确定和修改window对象位置的属性和方法又很多。IE、Safari、Opera和Chrome都提供了screenLeft和screenTop属性。分别用于表示窗口相对于屏幕左边和上边的位置。Firefox则在screenX和screenY属性中提供相同的窗口位置信息，Safari和Chrome也同事支持者两个属性。Opera虽然也支持者两个属性，但和screenLeft和screenTop属性并不对应，因此建议不要在Opera中使用它们。

```javascript
var leftPos = (typeof window.screenLeft == "number") ?
                window.screenLeft : window.screenX;
var topPos = (typeof window.screenTop == "number") ?
                window.screenTop : window.screenY;
```
- 这个例子运用二院操作符首先确定screenLeft和screenTop属性是否存在，如果存在则取这两个属性的值，如果不存在则取screenX和screenY的值。
- 在不同浏览器中，取得的值都不一定相同，所以无法在跨浏览器的条件下取得窗口左边和上边的精确坐标值。

##### 8.1.4 窗口大小
- 跨浏览器确定一个窗口的大小不是一件简单的事。IE9+、Firefox、Safari、Opera和Chrome均为此提供了4个属性：innerWidLh、innerHeight、outerWidth和outerHeight。
- 在IE9+、Firefox和Safari中，outerWidth和outerHeight返回浏览器窗口本身的尺寸（无论是从最外层的window对象还是从某个框架访同）。
- 在Opera中，这outerWidth和outerHeight两个属性的值表示页面视图容器的大小。而innerWidLh和innerHeight则表示该容器中页面视图区的大小（减夫边框宽度)。
- 在Chrome中，innerWidLh、innerHeight与outerWidth、outerHeight返回相同的值，即视口(viewport)大小而非浏览器窗口大小。
- 在IE、Firefox、Safari、Opera和Chrome中。document.documentElement.clientWidth和document.documentElement.clientHeight中保存了页面视扣的信息。
- 使用resizeTo()和resizeBy()方法可以调整浏览器窗口的大小，这两个方法都接收两个参数，其中resizeTo()接收浏览器窗口的新宽度和新高度，而resizeBy()接收新窗口与原窗口的宽度和高度之差。这两个方法也有可能被浏览器禁用，在Opera和IE7（及更高版本）中默认就是禁用的。

##### 8.1.5 导航打开窗口
- 使用window.open()方法既可以导航到一个特定的URL，也可以打开一个新的浏览器窗口。这个方法可以接受4个参数：要加载的URL、窗口目标、一个特性字符串以及一个表示新页面是否取代浏览器历史记录中当前加载页面的布尔值。通常只需传递第一个参数，最有一个参数只在不打开新窗口的情况下使用。

###### 1. 弹出窗口
- 如果给window.open()传递的第二个参数不是以个已经存在的窗口或框架，那么该方法就会分局在第三个参数位置上传入的字符串创建一个新窗口或新标签页。如果没有传入第三个参数，那么会打开一个带有全部默认设置（工具栏、地址栏和状态栏等）的新浏览器窗口（或者打开一个新标签页——根据浏览器设置）。在不打开新窗口的情况下，会忽略第三个参数。
- 第三个参数是一个逗号分隔的设置字符串，表示在新窗口中都显示哪些特性。下表列出了可以出现在这个字符串中的设置选项。

设置 | 值 | 说明
--- |:---:| ---
fullscreen|yes或no|表示浏览器窗口是否最大化。仅限IE
height|数值|表示新窗口的高度。不能小于100
left|数值|表示新窗口的左坐标。不能是负值
location|yes或no|表示是否在浏览器窗口中显示地址栏。
menubar|yes或no|表示是否在浏览器窗口中显示菜单栏。默认为no
resizeable|yes或no|表示是否可以通过拖动浏览器窗口的边框改变其大小。默认为no
scrollbars|yes或no|表示如果内容在视口中显示不下，是否允许滚动。默认为no
status|yes或no|表示是否在浏览器中显示状态栏。默认为no
toolbar|yes或no|表示是否在浏览器中显示工具栏。默认为no
top|数值|表示新窗口的上坐标。不能是负值
width|数值|表示新窗口的宽度。不能小于100

- 表中所列的部分或全部设置选项，都可以通过逗号分隔的名值对来指定，其中，名值对以等号表示，整个特性字符串中部允许出现空格。
- 新创建的window对象有一个opener属性，其中保存着打开它的原始窗口对象。

###### 2. 安全限制
- 有些浏览器为了安全在弹出窗口配置方面增加了限制。

###### 3. 弹出窗口屏蔽程序
- 大多数浏览器内置有弹出窗口屏蔽程序，而没有内置此类程序的浏览器，也可以安装第三方屏蔽程序。

##### 8.1.6 间歇调用和超时调用
- JavaScript是单线程语言，但它允许通过设置超时值和间歇时间值来调度代码在特定的时刻执行。前者是在指定的时间过后执行代码，而后者是每间隔指定的时间就执行一次代码。
- 超时调用需要使用window对象的setTimeout()方法，它接受两个参数：要执行的代码和以毫秒表示的时间（即在执行代码前需要等待多少毫秒）。其中，第一个参数可以是一个包含JavaScript代码的字符串（就和在eval()函数中使用的字符串一样），也可以是一个函数。例如：

```javascript
// 不建议传递字符串！
setTimeout("alert('hello!') ", 1000);
// 推荐的调用方式
setTimeout(function() {
    alert("hello!");
}, 1000);
```
- 虽然这两种调用方法都没有问题，但由于传递字符串可能导致性能损失，因此不建议以字符串作为第一个参数。
- 第二个参数是一个表示等待多长时间的毫秒数，但经过该时间后制定的代码不一定会执行。JavaScript是一个单线程序的解释器，因此一定时间内只能执行一段代码。为了控制要执行的代码，就有一个JavaScript任务列队。这些任务会按照它们添加带列队的顺序执行。setTimeout()的第二个参数告诉JavaScript再过多长时间把当前任务添加到列队中。如果列队是空的，那么添加的代码会立即执行；如果列队不是空的，那么它就要等前面的代码执行完了以后再执行。
- 调用setTimeout()之后，该方法会返回一个数值ID，表示超时调用。这个超时调用ID是计划执行代码的位移标识符，可以通过它来取消超时调用。要取消尚未执行的超时调用计划，可以调用clearTimtout()方法并将相应的超时调用ID作为参数传递给它。
- 超时调用的代码都是在全局作用域中执行的，因此函数中的this的值在非严格模式下指向window，在严格模式下是undefined。
- 间歇调用与超时调用类似，只不过它会按照执行的时间间隔重复执行代码，直至间歇调用被取消或者页面被卸载。
- 设置间歇调用的方法是setInterval()，它接受的参数与setTimeout()相同。
- setInterval()方法同样也会返回一个数值ID，和setTimeout()一样，也可以调用clearInterval()方法并传入这个ID取消间歇调用。

##### 8.1.7 系统对话框
- 浏览器通过alert()、confirm()和prompt()方法可以调用系统对话框向用户显示信息。
- alert()方法接受一个字符串。向用户显示一个系统对话框，其中包含指定的文本和一个OK("确定")按钮。
- confirm()方法除了一个OK按钮还会显示一个Cancel("取消")按钮，两个按钮可以让用户决定是否执行给定的操作。
- confirm()方法返回布尔值：true表示单击了OK，false表示单击了Cancel或者单击了右上角的X按钮。如下：

```javascript
if (confirm("Are you sure?")) {
    // OK
} else {
    // cancel or close
}
```
- prompt()方法是一个“提示”框，用于提示用户输入一些文本。提示框中除了显示OK和Cancel按钮之外，还会显示一个文本输入域，以供用户在其输入内容。
- prompt()方法接受两个参数：要显示给用户的文本提示和文本输入域的默认值（可以是一个空字符串）。
- 如果用户单击了OK则prompt()返回文本输入域的值，如果单击了Cancel或者关闭了对话框，则返回null，下面是例子：

```javascript
var result = prompt("What is your name?", "");
if (result !== null) {
    alert("Welcome, " + result);
}
```

#### 8.2 location对象
- location既是window对象的属性，也是document对象的属性。换句话说，window.location和document.location引用的是同一个对象。下表列出location对象的所有属性。

属性名 | 例子 | 说明
--- | --- | ---
hash|"#contents"|返回URL中的hash（#号后跟零或多个字符），如果URL中部包含散列，则返回空字符串
host|"www.wrox.com:80"|返回服务器名称和端口号（如果有）
hostname|"www.wrox.com"|返回不带端口号的服务器名称
href|"http://www.wrox.com"|返回当前加载页面的完整URL。而location对象的toString()方法也是返回这个值
pathname|"/WileyCDA/"|返回URL中的目录和（或）文件名
prot|"8080"|返回URL中指定的端口号。如果URL中不包含多口号，则返回空字符串
protocol|"http:"|返回页面使用的协议。通常是http:或https:
search|"?q=javascript"|返回URL的查询字符串。这个字符串以问号开头

##### 8.2.1 查询字符串参数
- 可以创建一个函数，用以解析查询字符串，然后返回包含所有参数的一个对象：

```javascript
function getQueryStringArgs() {
    // 取得查询字符串并去掉开头的
    var qs = (location.search.length > 0 ? location.search.substring(1) : ""),
    // 保存数据的对象
    args = {},
    // 取得每一项
    items = qs.length ? qs.split("&") : [],
    item = null,
    name = null,
    value = null,
    // 在for循环中使用
    i = 0,
    len = items.length;
    // 逐个将每一项添加到args对象中
    for (i = 0; i < len; i++) {
        item = items[i].split("=");
        name = decodeURLComponent(item[0]);
        value = decodeURLComponent(item[1]);
        if (name.length) {
            args[name] = value;
        }
    }
    return args;
}
```

##### 8.2.2 位置操作
- 使用location对象可以通过很多方式来改变浏览器的位置。首先也是最常用的方式，就是使用assign()方法并为其传递一个URL。如下所示：

```javascript
location.assign("http://www.wrox.com");
// location.href或window.location也会调用assign()方法
// 下面两行代码与显示调用assign()方法的效果完全一样
window.location = "http://www.wrox.com";
location.herf = "http://www.wrox.com";
```
- 当通过上述任何一种方式修改URL之后，浏览器的历史记录中就会生成一条新记录，因此用户单击“后退”按钮都会导航到前一个页面，要禁用这种行为，可以使用replace()方法。这个方法只接受一个参数，即要导航到的URL。
- reload()方法的作用是重新加载当前显示的页面。

```javascript
location.reload(); // 重新加载（有可能从缓存中加载）
location.reload(true); // 重新加载（从服务器重新加载）
```

#### navigator对象
- 每个浏览器中的navigator对象都有一套自己的属性。

##### 8.3.1 检测插件
- 检测浏览器中是否安装了特定的插件是一种常见的检测例程。对于非IE浏览器，可以使用plugins数组来达到这个目的。该数组中的每一项都包含下列属性。
  - name：插件的名字
  - description：插件的描述
  - filename：插件的文件名
  - length：插件所处理的MIME类型数量。
- 一般来说，name属性中会包含检测插件必须的所有信息，但有时候也不必完全如此。在检测插件时，需要像下面这样循环迭代每个插件并将插件的name与给定的名字比较。

```javascript
// 检测插件(在IE中无效)
function hasPlugin(name) {
    name = name.toLowerCase();
    for (var i = 0; i < navigator.plugins.length; i++) {
        if (navigator.plugins[i].name.toLowerCase().indexOf(name) > -1) {
            return true;
        }
    }
    return false;
}
// 检测Flash
alert(hasPlugin("Flash"));
// 检测QuickTime
alert(hasPlugin("QuickTime"));

// 检测IE中的插件
function hasIEPlugin(name) {
    try {
        new ActiveXObject(name);
        return true;
    } catch (ex) {
        return false;
    }
}
// 检测Flash
alert(hasIEPlugin("ShockwaveFlash.ShockwaveFlash"));
// 检测QuickTime
alert(hasIEPlugin("QuickTime.QuickTime"));
```
- 鉴于检测这两种插件的方法差别太大，因此典型的做法是针对每个插件分别创建检测函数，而不是使用前面介绍的通用检测方法。例子如下：

```javascript
// 检测所有浏览器中的Flash
function hasFlash() {
    var result = hasPlugin("Flash");
    if (!result) {
        result = hasIEPlugin("ShockwaveFlash.ShockwaveFlash");
    }
    return result;
}
// 检测所有浏览器中的QuickTime
function hasQuickTime
     var result = hasPlugin("QuickTime");
    if (!result) {
        result = hasIEPlugin("QuickTime.QuickTime");
    }
    return result;
}
```
- plugins集合有一个名叫refresh()的方法，用于刷新plugins以反映最新安装的插件。这个方法接受一个参数：标识是否应该重新加载页面的一个布尔值。如果将这个值设置为true，则会重新加载包含插件的所有页面；否则，只更新plusins集合，不重新加载页面。


##### 8.3.2 注册处理程序
- Firefox2为navigator对象新增了registerContentHandler()和registerProtocolHandler()方法。这两个方法可以让一个站点指明它可以处理的特定类型的信息。
- registerContentHandler()方法接受三个参数：要处理的MIME类型、可以处理该MIME类型的页面的URL以及应用程序 的名称。
- registerProtocolHandler()方法也接受三个参数：要处理的协议（例如mailto或ftp）、处理该协议的页面的URL和应用程序的名称。

#### 8.4 screen对象
- JavaScript中有几个对象在变成中用处不大，而screen对象就是其中之一。screen对象基本上只用来表明客户端的能力，其中包括浏览器窗口外部的显示器的信息，如像素宽度和高度等等。每个浏览器中的screen对象都包含着各个不相同的属性。

#### 8.5 history对象
- history对象保存着用户上网的历史记录，从窗口被打开的那一刻算起。因为history是window对象的属性，因此每个浏览器窗口、每个标签页乃至每个框架，都有自己的history对象与特定的window对象关联。处于安全方面的考虑，开发人员无法得知用户浏览过的URL。不过借由用户访问过的页面列表，同样可以在不知道实际URL的情况下实现后退和前进。
- 使用**go()** 方法可以在用户的历史记录中任意跳转，可以向后也可以向前。这个方法接受一个参数，标识向后或向前跳转的页面数的一个整数值。负数标识向后跳转（类似于单机浏览器的“后退”按钮），正数标识向前跳转（类似于单机浏览器的“前进”按钮）。

```javascript
// 后退一页
history.go(-1);
// 前进一页
history.go(1);
// 前进两页
history.go(2);
```
- 也可以给go()方法传递一个字符串参数，此时浏览器会跳转到历史记录中包含该字符串的第一个位置——可能后退，也可能前进，具体要看哪个位置最近。如果历史记录中不包含该字符串，那么这个方法什么也不做。例如

```javascript
// 跳转到最近的wrox.com页面
history.go("wrox.com");
```
- 另外，还可以使用两个简单方法back()和forword()来代替go()。

```javascript
// 后退一页
history.back();
// 前进一页
history.forward();
```
- history对象还有一个length属性，保存着历史记录的数量。这个数量包括所有历史记录，即所有向后和向前的记录。
