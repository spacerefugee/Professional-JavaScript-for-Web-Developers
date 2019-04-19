### 第13章 事件

JavaScript与HTML自己的交互是通过事件实现的。事件，就是文档或浏览器窗口中发送的一些特定的交互瞬间。可以使用侦听器（或处理程序）来预订事件，以便事件发生时执行相应的代码。这种在传统软件工程中北成为观察员模式的模型，支持页面的行为（JavaScript代码）与页面的外观（HTML和CSS代码）之间的松散耦合。

#### 13.1 事件流

**事件流**描述的是从页面中接收事件的顺序。IE和Netscape的事件流的概念差不多完全相反。IE的事件流是事件冒泡流，而Netscape Communicator的事件流是事件捕获流。

##### 13.1.1 事件冒泡

**事件冒泡**(event bubbling)，即事件开始时由最具体的元素（文档中嵌套层次最深的那个节点）接收，然后逐级向上传播到较为不具体的节点（文档）。

##### 13.1.2 事件捕获

**事件捕获**(event capturing)的思想是不太具体的节点应该更早接收到事件，而最具体的节点应该最后接收到事件。事件捕获的用意在于在时间到达预订目标之前捕获它。

由于老版本的浏览器不支持，因此很少有人使用事件捕获。建议使用事件冒泡，在有特殊需要时再使用事件捕获。

##### 13.1.3 DOM事件流

“DOM2级事件”规定的事件流包括三个阶段：事件捕获阶段、处于目标阶段和事件冒泡阶段。首先发生的是事件捕获，为截获事件提供了机会。然后是实际的目标接收到事件。最后一个阶段是冒泡阶段，可以在这个阶段对事件作出响应。

#### 13.2 事件处理程序

事件就是用户或浏览器自身执行的某种动作。诸如click、load和mouseover，都是事件的名字。而响应某个事件的函数就叫做事件处理程序（或事件侦听器）。事件处理程序的名字以"on"开头，因此click事件的事件处理程序就是onclick，load事件的事件处理程序就是onload。为事件指定处理程序的方式有好几种。

##### 13.2.1 HTML事件处理程序

某个元素支持的每种事件，都可以使用一个与相应事件处理程序同名的HTML特性来指定。这个特性的值应该是能够执行的JavaScript。例如，要在按钮被单击时执行一些JavaScript，可以像下面这样编写代码：

```html
<imput type="button" value="Click Me" onclick="alert{'Click'}" />
// 也可以调用页面其他地方定义的脚本
<imput type="button" value="Click Me" onclick="showMessage()" />
<script type="text/javascript">
    function showMessage() {
        alert("hello world!");
    }
</script>
```

这样指定事件处理程序具会创建一个封装着元素属性值的函数。这个函数中有一个局部变量event，也就是事件对象。通过event对象，可以直接访问事件兑现，不用自己定义它也不用从函数的参数列表中读取。

在HTML中指定事件处理程序有几个缺点：

- 存在时差问题。如果HTML元素一出现在页面上就触发相应的事件，但当时的事件处理程序有可能尚不具备执行条件，就会引发错误
- 这样扩展事件处理程序的作用域链在不同浏览器中会导致不同结果。不同JavaScript引擎遵循的标识符解析规则略有差异，很可能会在访问非限定对象成员时出现错误。
- HTML与JavaScript代码紧密耦合。如果要更换时间处理程序，就要改动两个地方：HTML代码和JavaScript代码。而这正是许多开发人员摒弃HTML事件处理程序，转而使用JavaScript指定事件处理程序的原因所在。

##### 13.2.2 DOM0级事件处理程序

通过JavaScript指定事件处理程序的传统方式，就是将一个函数赋值给一个事件处理程序属性。这种行为事件处理程序赋值的方法是在第四代web浏览器中出现的，而且至今仍然为所有现代浏览器所支持。原因一是简单，二是具有跨浏览器的优势。要使用JavaScript指定事件处理程序，首先必须取得一个要操作的对象的引用。

每个元素（包括window和document）都有自己的事件处理程序属性，这些属性通常全部小写，例如onclick。将这种属性的值设置为一个函数，就可以指定事件处理程序。如下所示：

```javascript
var btn = document.getElementById("myBtn");
btn.onlick = function() {
    alert("click!");
    // 使用DOM0级方法指定的事件处理程序被认为是元素的方法。
    // 因此，这时候的事件处理程序时在元素的作用域中运行；
    // 换句话说，程序中的this引用当前元素。
    alert(this.id); // "myBtn"
}
// 也可以通过删除DOM0级方法指定的事件处理程序
btn.onclick = null; // 删除事件处理程序
```

##### 13.2.3 DOM2级事件处理程序

“DOM2级事件”定义了两个方法，用于处理指定和删除事件处理程序的操作：
- addEventListener()：添加事件处理程序
- removeEventListener()：移除事件处理程序

所有DOM节点中都包含着两个方法，并且它们都接受3个参数：

- 要处理的事件名
- 作为事件处理程序的函数
- 一个布尔值：如果是true，表示在捕获阶段调用事件处理程序；如果是false，表示在冒泡阶段调用事件处理程序

要在按钮上为click事件添加事件处理程序，可以使用下列代码：

```javascript
var btn = document.getElementById("myBtn");
btn.addEventListener("click", function() {
	alert(this.id);   
}, false);
// 使用DOM2级方法添加事件处理程序可以添加多个事件处理程序
btn.addEventListener("click", function() {
    alert("hello world!");
}, false);
// 两个事件处理程序会按照添加它们的顺序触发
```

通过addEventListener()添加的事件处理程序只能使用removeEventListener()来移除；移除传入的参数与添加处理程序时使用的参数相同。这也意味着通过addEventListener()添加的匿名函数将无法移除。

```javascript
btn.removeEventListener("click", function() { // 没有用！
    alert("this.id");
}, false);
// 有效的做法
var handler = function() {
    alert(this.id);
}
btn.addEventListener("click", handler, false);
btn.removeEventListener("click", handler, false); // 有效！
```

##### 13.2.4 IE事件处理程序

IE实现了与DOM中类似的两个方法：

- attachEvent()：添加事件处理函数
- detachEvent()：移除事件处理函数

这两个方法接收相同的两个参数：

- 事件处理程序名称
- 事件处理程序函数

由于IE8及更早版本只支持事件冒泡，所以通过attachEvent()添加的时间处理程序都会被添加到冒泡阶段。

###### 13.2.5 跨浏览器的事件处理程序

要保证处理事件的代码能在大多数浏览器下一致地运行，只需关注冒泡阶段。

- 第一个要创建的方法是addHandler()，它的职责是视情况分别使用DOM0级方法、DOM2级方法或IE方法来添加事件。这个方法属于一个名叫EventUtil的对象。addHandler()方法接收三个参数：要操作的元素、事件名称和事件处理函数。
- 与addHandler()对应的方法是removeHandler()，它也接收相同的参数。这个方法的职责是移除之前添加的时间处理程序——无论该事件处理程序时采取什么方式添加到元素重点 ，如果其他方法无效，默认采用DOM0级方法。

EventUtit的用法如下所示：

```javascript
var EventUtil = {
	addHandler : function(element, type, handler) {
        if (element.addEventListener) {
            element.addEventListener(type, handler, false);
        } else if (element.attachEvent) {
            element.attachEvent("on" + type, handler);
        } else {
            element["on" + type] = handler;
        }
    },
    removeHandler : function(element, type, handler) {
        if (element.removeEventListener) {
            element.removeEventListener(type, handler, false);
        } else if (element.detachEvent) {
            element.detachEvent("on" + type, handler);
        } else {
            element["on" + type] = null;
        }
    }   
}
```

addHandler()和removeHandler()没有考虑到所有浏览器问题，例如在IE中作用域问题。不过，使用它们添加和移除事件处理程序还是足够了。此案为还要注意，DOM0级对每个事件只支持一个事件处理程序。好在只支持DOM0级的浏览器已经没有那么多了。

#### 13.3 事件对象

在触发DOM上的某个事件时，会产生一个事件对象event，这个对象中包含着所有与事件有关的信息。包括导致事件的元素、事件的类型以及其他与特定事件相关的信息。例如，鼠标操作导致的事件对象中，会包含鼠标位置的信息，而键盘操作导致的事件中，会包含于按下的键有关的信息。所有浏览器都支持event对象，但支持方式不同。

##### 13.3.1 DOM中的事件对象

兼容DOM的浏览器会将一个event对象传入到事件处理程序中。无论指定事件处理程序时使用什么方法（DOM0级或DOM2级），都会传入event对象。

event对象包含与创建它的特定事件有关的属性和方法。触发的事件类型不一样，可以用的属性和方法也不一样。不过，所有事件都会有下表列出的成员。

| 属性/方法                  | 类型         | 说明                                                         |
| :------------------------- | :----------- | :----------------------------------------------------------- |
| bubbles                    | Boolean      | 表面事件是否冒泡                                             |
| cancelable                 | Boolean      | 表面是否可以取消事件的默认行为                               |
| cruuentTarget              | Element      | 某事件处理程序当前正在处理事件的那个元素                     |
| defaultPrevented           | Boolean      | 为true表示已经调用preventDefault()                           |
| detail                     | Integer      | 与事件相关的细节信息                                         |
| eventPhase                 | Integer      | 调用事件处理程序的阶段：1表示捕获阶段，2表示“处于目标”，3表示冒泡阶段 |
| preventDefault()           | Function     | 取消事件的默认行为。如果cancelable是true，则可以使用这个方法 |
| stopImmediatePropagetion() | Function     | 取消事件的进一步捕获或冒泡，同时组织任何事件处理程序被调用   |
| stopPropagation()          | Function     | 取消事件的进一步捕获或冒泡，如果bubbles为true，则可以使用这个方法 |
| target                     | Element      | 事件的目标                                                   |
| trusted                    | Boolean      | 为true表示事件时浏览器生成的。为false表示事件是由开发人员通过JavaScript创建的 |
| type                       | String       | 被触发的事件类型                                             |
| view                       | AbstractView | 与事件关联的抽象视图。等同于发生事件的window对象             |

##### 13.3.2 IE中的事件对象

与访问DOM中的event对象不同，要访问IE中的event对象有几种不同的方式，取决于指定事件处理程序的方法。在使用DOM0级方法添加事件处理程序时，event对象作为window对象的一个属性存在。

##### 13.3.3 跨浏览器的事件对象

虽然DOM和IE中的event对象不同，但基于它们之间的相似性依旧可以拿出跨浏览器的方案来。IE中event对象的全部信息和方法DOM对象中都有，只不过实现方式不一样。不过这种对应关系让实现两种模型之间的映射非常容易。可以对EventUtil对象加以增强：

```javascript
var EventUtil = {
	addHandler : function(element, type, handler) {
        if (element.addEventListener) {
            element.addEventListener(type, handler, false);
        } else if (element.attachEvent) {
            element.attachEvent("on" + type, handler);
        } else {
            element["on" + type] = handler;
        }
    },
    getEvent : function(event) {
        return event ? event : window.event;
    },
    getTarget : function(event) {
        return event.target || event.srcElement;
    },
    preventDefault : function(event) {
        if (event.preventDefault) {
            event.preventDefault();
        } else {
            event.returnValue = false;
        }
    },
    removeHandler : function(element, type, handler) {
        if (element.removeEventListener) {
            element.removeEventListener(type, handler, false);
        } else if (element.detachEvent) {
            element.detachEvent("on" + type, handler);
        } else {
            element["on" + type] = null;
        }
    },
    stopPropagation : function(event) {
        if (event.stopPropagation) {
            event.stopPropagation();
        } else {
            event.cancelBubble = true;
        }
    }
};
```

#### 13.4 事件类型

Web浏览器中可能发生的事件有很多类型。如前所述，不同的事件类型具有不同的信息，而“DOM3级事件”規定了以下几类事件。

- UI（User Interface，用户界面）事件，当用户与页面上的元素交互时触发；
- 焦点事件，当元素获得或失去焦点时触发；
- 鼠标事件，当用户通过鼠标在页面上执行操作时触发；
- 滚轮事件，当使用鼠标滚轮（或类似设备）时触发；
- 文本事件，当在文档中输人文本时触发；
- 键盘事件，当用户通过键盘在页面上执行操作时触发；
- 合成事件，当为IME(Input Method Editor，输人法编辑器）输人字符时触发；
- 变动（mutation)事件，当底层DOM结构发生变化时触发。
- 变动名称事件，当元素或属性名变动时触发。此类事件已经被废弃，没有任何浏览器实现它们。

##### 13.4.1 UI事件

UI事件指的是那些不一定与用户操作有关的事件。这些事件在DOM规范出现之前，都是以这种或那种形式存在的，而在DOM规范中保留是为了向后兼容。现有的UI事件如下：

- DOMActivate：表示元素已经被用户操作（通过鼠标或键盘）激活。这个时间在DOM3级事件中被废弃，但Firefox 2+和Chrome支持它。考虑到不同浏览器实现的差异，不建议使用这个事件。
- load：当页面完全加载后在window上触发，当所有框架都加载完毕时在框架集上面触发，当图像加载完毕时在\<img\>元素上面触发，或者当嵌入的内容加载完毕时在\<object\>元素上面触发
- unload：当页面完全卸载后在window上面触发，当所有框架都卸载后在框架集上面触发，或者当嵌入的内容卸载完毕后在\<object\>元素上面触发。
- abort：在用户停止下载过程时，如果嵌入的内容没有加载完，则在\<object\>元素上面触发。
- error：当发生JavaScript错误时在window上面触发，当无法加载图像时在\<img\>元素上面触发，当无法加载嵌入内容是\<object\>元素上面触发，或者当有一或多个框架无法加载时在框架上面触发。
- select：当用户选择文本框（\<input\>或\<texterea\>）中的一或多个字符时触发。
- resize：当窗口或框架的大小变化时在window或框架上面触发。
- scroll：当用户滚动带滚动条的元素中的内容时，在该元素上面触发。\<body\>元素中包含所有加载页面的滚动条。

##### 13.4.2 焦点事件

焦点事件会在页面获得或失去焦点时触发。利用这些事件并与document.hasFocus()方法及document.activeElement属性配合，可以知晓用户在页面上的行踪。有以下6个焦点事件。

- blur：在元素失去焦点时触发。这个事件不会冒泡；所有浏览器都支持它。
- DOMFocusIn：在元素获得焦点时触发。这个事件与HTML事件focus等价，但它冒泡。只有Opera支持这个事件。DOM3级事件废弃了DOMFocusIn，选择了focusin。
- DOMFocusOut：在元素失去焦点时触发。这个事件是HTML事件blur的通用版本。只有Opera支持这个事件。DOM3级事件废弃了DOMFocusOut，选择了focusout。
- focus：在元素获得焦点时触发。这个事件不会冒泡；所有浏览器都支持它。
- focusin：在元素获得焦点时触发。这个事件与HTML事件focus等价，但它冒泡。支持这个事件的浏览器有：IE 5.5+、Safari 5.1+、Opera 11.5+和Chrome。

当焦点从页面的一个元素移动到另一个元素，会一次触发下列事件：

1. focusout在失去焦点的元素上触发；
2. focusin在获得焦点的元素上触发；
3. blur在失去焦点的元素上触发；
4. DOMFocusOut在失去焦点的元素上触发；
5. focus在获得焦点的元素上触发；
6. DOMFocusIn在获得焦点的元素上触发。

##### 13.4.3 鼠标与滚轮事件

鼠标事件是Web开发中最常用的一类事件，毕竟鼠标还是最主要的定位设备。DOM3级事件中定义了9个鼠标事件，简介如下：

- click：在用户单击主鼠标按钮（一般是左边的按钮）或者按下回车键时触发。这一点确保易访问性很重要，意味着onclick事件处理程序既可以通过键盘也可以通过鼠标执行。
- dblclick：在用户双击主鼠标按钮（一般是左边的按钮）时触发。从技术上说，这个事件并不是DOM2级事件规范中规定的，但鉴于它得到了广泛支持，所以DOM3级事件将其纳入了标准。
- mousedown：在用户按下了任意鼠标按钮时触发。不能通过键盘触发这个事件。
- mouseenter：在鼠标光标从元素外部首次移动到元素范围之内时触发。这个事件不冒泡，并且在光标移动到后代元素上不会触发。DOM2级事件并没有定义这个事件，但DOM3级事件将它纳入了规范。IE、Firefox 9+和Opera支持这个事件。
- mouseleave：在位于元素上方的鼠标光标移动到元素范围之外时触发。这个事件不冒泡，并且在光标移动到后代元素上不会触发。DOM2级事件并没有定义这个事件，但DOM3级事件将它纳入了规范。IE、Firefox 9+和Opera支持这个事件。
- mousemove：当鼠标指针在元素内部移动时重复地触发。不能通过键盘触发这个事件。
- mouseout：在鼠标指针位于一个元素上方，然后用户将其移入另一个元素时触发。又移入的另一个元素可能位于前一个元素的外部，也可能是这个元素的子元素。不能通过键盘触发这个事件。
- mouseover：在鼠标指针位于一个元素外部，然后用户将其首次移入另一个元素边界之内时触发。不能通过键盘触发这个事件。
- mouseup：在用户释放鼠标按钮时触发。不能通过键盘触发这个事件。

页面上的所有元素都支持鼠标事件。除了mouseenter和mouseleave，所有鼠标事件都会冒泡，也可以被取消，而取消鼠标事件将会影响浏览器的默认行为。取消鼠标事件的默认行为还会影响其他事件，因为鼠标事件与其他事件是密不可分的关系。
只有在同一个元素上相继触发mousedown和mouseup事件，才会触发click事件；如果mousedown或mouseup中的一个被取消，就不会触发click事件。类似地，只有触发两次click事件，才会触发一次dblclick事件。如果有代码阻止了连续两次触发click事件（可能是直接取消click事件，也可能通过取消mousedown或mouseup间接实现），那么就不会触发dblclick事件了。这4个事件触发的顺序始终如下：

1. mousedown
2. mouseup
3. click
4. mousedown
5. mouseup
6. click
7. dblclick

##### 13.4.4 键盘与文本事件

有3个键盘事件：

- keydown：当用户按下键盘上的任意键时触发，而且如果按住不放的话，会重复触发此事件。
- keypress：当用户按下键盘上的字符键时触发，而且如果按住不放的话，会重复触发此事件。按下Esc键也会触发这个事件。Safari3.1之前的版本也会在用户按下非字符键时触发keypress事件。
- keyup：当用户释放键盘上的键时触发。

##### 13.4.5 复合事件

复合事件(composition event)是DOM3级事件中新添加的一类事件，用于处理IME的输人序列 IME(Input Method Editor，输人法编辑器）可以让用户输人在物理键盘上找不到的字符。例如，使用拉丁文键盘的用户通过IME照样能输人日文字符。IME通常需要同时按住多个键，但最终只输人一个字 符。复合事件就是针对检测和处理这种输人而设计的。有以下三种复合事件。 

- compositionstart：在IME的文本复合系统打开时触发，表示要开始输人了。
- compositionupdate：在向输入字段中插入新字符时触发。
- compositionend：贼IME的文本复合系统关闭时触发，表示返回正常键盘输入状态。

复合事件与文本事件在很多方面都很相似。在触发复合事件时，目标是接收文本的输入字段。但它比文本事件的事件对象多一个属性data，其中包含以下几个值中的一个：

- 如果在compositionstart事件发生时访问，包含正在编辑的文本（例如，已经选中的需要马上替换的文本）；
- 如果在compositionupdate事件发生时访问，包含正插入的新字符；
- 如果在compositionend事件发生时访问，包含此次输入会话中插入的所有字符。

##### 13.4.6 变动事件

DOM2级的变动（mutation）事件能在DOM中的某一部分发生变化时给出提示。变动事件是为了XML或HTML DOM设计的，并不特定于某种语言。DOM2级定义了如下变动事件。

- DOMSubtreeModified：在DOM结构中发生任何变化时触发。这个事件在其他任何事件触发后都会触发。
- DOMNodeInserted：在一个节点作为子节点被插入到另一个节点中时触发。
- DOMNodeRemoved：在节点从其父节点中被移除时触发。
- DOMNodeInsertedIntoDocument：在一个节点被直接插入文档或通过子树简介插入文档之后触发。这个事件在DOMNodeInserted之后触发。
- DOMNodeRemovedFromDocument：在一个节点被直接从文档中移除或通过子树简介从文档中移除之前触发。这个事件在DOMNodeRemoved之后触发。
- DOMAttrModified：在特性被修改之后触发。
- DOMCharacterDataModified：在文本节点的值发生变化时触发。

##### 13.4.7 HTML5事件

HTML5详尽列出了浏览器应该支持的所有事件，以下是得到浏览器完善支持的事件：

- contextmenu事件：用以表示何时应该显示上下文菜单，以便开发人员取消默认的上下文菜单而提供自定义的菜单。这个事件是冒泡的。
- beforeunload事件：这个事件会在浏览器卸载页面之前触发，可以通过它来取消卸载并继续使用原有页面。
- DOMContentLoaded事件：这个事件在行程完整的DOM树之后会触发，不理会图像、JavaScript文件、CSS文件或其他资源是否已经下载完毕。
- readystatechange事件：这个事件的目的是提供与文档或元素的加载状态有关的信息。
- pageshow和pagehide事件：pageshow在压面显示时触发，无论该页面是否来自bfcache。pagehide会在浏览器卸载页面时触发，而且是在unload事件之前触发。
- hashchange事件：这个事件用于在URL的参数列表（及URL中“#”号后面的所有字符串）发生变化时通知开发人员。

##### 13.4.8 设备事件

设备事件（device event）可以让开发人员确定用户在怎样使用设备。

- orientationchange事件：这个事件可以让开发人员确定一用户何时将设备由横向查看模式切换为纵向查看模式。
- MozOrientation事件：（Moz表示这个是特定于浏览器开发商的事件，不是标准事件）当设备的加速计检测到设备方向改变时，就会触发这个事件。
- deviceorientation事件：该事件也是在加速计检测到设备方向变化时在window对象上触发，而且具有与MozOrientation事件相同的支持限制。
- devicemotion事件：这个事件是要告诉开发人员设备什么时候移动，而不仅仅是设备方向如何改变。

##### 13.4.9 触摸与手势事件

###### 1. 触摸事件

- touchstart：当手指触摸屏幕时触发；即使已经有一个手指放在了屏幕上也会触发。
- touchmove：当手指在屏幕上滑动时连续地触发。在这个事件发生期间，调用preventDefault()可以组织滚动。
- touchend：当手指从屏幕上移开时触发。
- touchcancel：当系统停止跟踪触摸时触发。

除了常见的DOM属性外，触摸事件还包含下列三个用于跟踪触摸的属性：

- touches：表示当前跟踪的触摸操作的Touch对象的数组。
- targetTouchs：特定于事件目标的Touch对象的数组。
- changeTouches：表示自上次触摸一来发生了什么改变的Touch对象的数组

每个Touch对象包含下列属性：

- chientX：触摸目标在视口中的x坐标。
- clientY：触摸目标在视口中的y坐标。
- indentifier：标识触摸的唯一ID。
- pageX：触摸目标在页面中的x坐标。
- pageY：触摸目标在页面中的y坐标。
- screenX：触摸目标在屏幕中的x坐标。
- screenY：触目目标在屏幕中的y坐标。
- target：触摸的DOM节点目标。

###### 2. 手势事件

- gesturestart：当一个手指已经按在屏幕上而另一个手指又触摸屏幕时触发。
- gesturechange：当触摸屏幕的任何一个手指的位置发生变化时触发。
- gestureend：当任何一个手指从屏幕上面移开时触发。

#### 13.5 内存和性能

在JavaScript中，添加到页面上的事件处理程序将直接关系到页面的整体运行性能。

##### 13.5.1 事件委托

事件委托利用了事件冒泡，只指定一个事件处理程序，就可以管理某一类的所有事件。

##### 13.5.2 移除事件处理程序

在不需要的时候移除事件处理程序，也是解决页面性能问题的一种方案。内存中留有那些过时不用的“空事件处理程序”（dangling event handler），也就是造成Web应用程序内存与性能问题的主要原因。

#### 13.6 模拟事件

还在测试Web应用程序，模拟触发事件是一种极其有用的技术。

##### 13.6.1 DOM中的事件模拟

可以在document对象上使用createEvent()方法创建event对象。这个方法接收一个参数，即表示要创建的事件类型的字符串。这个字符串可以是下列几个字符串之一：

- UIEvents：一般化的UI事件。鼠标事件和键盘事件都继承自UI事件。DOM3级中时UIEvent。
- MouseEvents：一般化的鼠标事件。DOM3级中时MouseEvent。
- MutationEvents：一般化的DOM变动事件。DOM3级中是MutationEvent。
- HTMLEvents：一般化的HTML事件。没有对应的DOM3级事件。

##### 13.6.2 IE中的事件模拟

调用document.createEventObject()方法可以在IE中创建event对象。这个方法不接收参数，结果会返回一个通用的event对象。然后必须手工为这个对象添加所有必要的信息。

