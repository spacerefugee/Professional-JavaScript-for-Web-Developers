### 第11章 DOM扩展

尽管DOM作为API已经非常完善了，但为了实现更多的功能，仍然会有一些标准或专有的扩展。  

#### 11.1 选择符API

众多JavaScript库中最常用的一项功能，就是根据CSS选择符选择与某个模式匹配的DOM元素。实际上JQuery的核心就是通过CSS选择符查询DOM文档取得元素的引用，从而抛开了getElementById()和getElementsByTagName()。

SelectorsAPI是由W3C发起指定的一个标准，致力于让浏览器原生支持CSS查询。所有实现这一功能的JavaScript库都会写一个基础的CSS解析器，然后再使用已有的DOM方法查询文档并找到匹配的节点。尽管库开发人员在不知疲倦地改进这一过程的性能，但到头来都只能通过运行JavaScript代码来完成查询操作。而把这个功能变成原生API之后，解析和数查询操作可以在浏览器内部通过编译后的代码来完成，极大地改善了性能。

##### 11.1.1 querySelector()方法

- querySelector()方法接收一个CSS选择符，返回与该模式匹配的第一个元素，如果没有找到匹配的元素，返回null。

  ```javascript
  // 取得body元素
  var body = document.querySelector("body");
  // 取得id为"myDiv"的元素
  var myDiv = document.querySelector("#myDiv");
  // 取得类为"selected"的第一个元素
  var selected = document.querySelector(".selected")；
  // 取得类为"button"的第一个图像元素
  var iiiiimg = document.body.querySelector("img.button");
  ```

- 通过Document类型调用querySelector()方法时，会在文档元素的范围内查找匹配的元素。而通过Element类型调用querySelector()方法时，只会在该元素后代元素的范围内查找匹配的元素。

##### 11.1.2 querySelectorAll()方法

- querySelectorAll()方法接收的参数与querySelector()方法一样，都是一个CSS选择符，但返回的是所有匹配的元素而不仅仅是一个元素。这个方法返回的是一个NodeList的实例。

- 只要传给querySelectorAll()方法的CSS选择符有效，该方法都会返回一个NodeList对象，而不管找到多少匹配的元素。如果没有找到匹配的元素，NodeList就是空的。

  ```javascript
  // 取得某<div>中的所有<em>元素（类似于getElementsByTagName("em")
  var ems = document.getElementById("myDiv").querySelectorAll("em");
  // 取得类为"selected"的所有元素
  var selecteds = document.querySelectorAll(".selected");
  // 取得所有<p>元素中所有<strong>元素
  var strongs = document.querySelectorAll("p strong")；
  ```

- 同样与querySelector()类似，入股哦传入了浏览器不支持的选择符或者选择符中有语法错误，querySelectorAll()会抛出错误。

##### 11.1.3 matchesSelector()方法

- SelectorsAPI Level2规范为Element类型新增了一个方法matchesSelector()。这个方法接收一个参数，即CSS选择符，如果调用元素与该选择符匹配，则返回true，否则返回false。

  ```javascript
  if (document.body.matchesSelector("body.page1")) {
      	// true
      }
  ```

- 在取得某个元素引用的情况下，使用这个方法能够方便地检测它是否会被querySelector()或querySelectorAll()方法返回。

#### 11.2 元素遍历

对于元素间的空格，IE9及之前版本不会返回文本节点，而其他所有浏览器都会返回文本节点。这样，就导致了再使用childNodes和firstChild等属性时的行为不一致。为了弥补这一差异，而同时又保持DOM规范不变，Element Traversal规范新定义了一组属性。

Element Traversal API为DOM元素添加了一下5个属性。

- ChildElementCount：返回子元素（不包括文本节点和注释）的个数。
- firstElementChild：指向第一个子元素；firstChild的元素版。
- lastElementChild：指向最有一个元素；lastChild的元素版。
- previousElementSibling：指向前一个同辈元素；previousSibling的元素版。
- nextElementSibling：指向后一个同辈元素；nextSibling的元素版。

#### 11.3 HTML5

HTML5规范则围绕如何使用新增标记定义了大量JavaScript API。其中一些API与DOM重叠，定义了浏览器应该支持的DOM扩展。

##### 11.3.1 与类相关的扩充

###### 1. getElementsByClassName()方法

- getElementsByClassName()方法接收一个参数，即一个包含一或多个雷鸣的字符串，返回带有指定类的所有元素的NodeList。传入多个类名时，类名的先后顺序不重要。
- 调用这个方法时，只有位于调用元素子树中的元素才会返回。在document对象上调用getElementsByClassName()始终会返回与类名匹配的所有元素，在元素上调用该方法就只会返回后代元素中匹配的元素。

###### 2. classList属性

- classList属性是新集合类型DOMTokenList的实例。与其他DOM集合类似。DOMTokenList有一个表示自己包含多少元素的length属性，而要取得每个元素可以使用item()方法，也可以使用方括号语法。此外，这个新类型还定义如下方法。
  - add(value)：将给定的字符串添加到列表中。如果值已经存在，就不添加了。
  - contains(value)：表示列表中是否存在给定的值，如果存在则范湖itrue，否则返回false。
  - remove(value)：从列表中删除给定的字符串。
  - toggle(value)：如果列表中已经存在给定的值。删除它；如果列表中没有给定的值，添加它。
- 有了classList属性，除非你需要全部删除所有类名，或者完全你重写元素的class属性，否则也就用不到className属性了。

##### 11.3.2 焦点管理

- HTML5也添加了辅助管理DOM焦点的功能，首先就是document.activeElement属性，这个属性始终会引用DOM中当前获得了焦点的元素。元素获得焦点的方式有页面加载、用户输入（通常是通过按Tab键）和在代码中调用focus()方法。
- document.hasFocus()方法用于确定文档是否获得了焦点。

##### 11.3.3 HTMLDocument的变化

HTML5扩展了HTMLDocument，增加了新的功能。与HTML5中新增的其他DOM扩展类似，这些变化同样基于那些已经得到很多浏览器完美支持的专有扩展。所以尽管这些扩展被写入标准的时间相对补偿，但很多浏览器很早就已经支持这些功能了。

###### 1. readyState属性

- readyState有两个可能的值：
  - loading，正在加载文档；
  - complete，已经加载完文档。
- 使用document.readyState可以实现一个指示文档已经加载完成的指示器。

###### 2. 兼容模式

- 在标准模式下，**document.compatMode** 的值等于"CSS1Compat"，而在混杂模式下，document.compatMode的值等于"BackCompat"。

###### 3. head属性

- 要引用文档的\<head\>元素，可以结合使用这个属性和另一种后背方法。

```javascript
var head document.head || document.getElementsByTagName("head")[0];
```

##### 11.3.4 字符集属性

- charset属性表示文档中实际使用的字符集，也可以用来指定新字符集。默认情况下这个属性的值为"UTF-16"，但可以通过\<meta\>元素、响应头部或直接设置charset属性修改这个值。

  ```javascript
  alert(document.charset); // "UTF-16"
  document.charset = "UTF-8";
  ```

- defaultCharset属性表示根据默认浏览器及操作系统的设置，当前文档默认的字符集应该是什么。如果文档没有使用默认的字符集，那charset和defaultCharset属性的值可能会不一样。

##### 11.3.5 自定义数据属性

- HTML5规定可以为元素添加非标准的属性，但要添加前缀data-，目的是为元素提供与渲染无关的信息，或者提供语义信息。

  ```html
  <div id="myDiv" date-appId="12345" data-myname="Nicholas"></div>
  ```

- 添加自定义属性之后，可以通过元素的dataset属性来访问自定义属性的值。dataset属性的值是DOMStringMap的一个实例，也就是一个名值对的映射。在这个映射中，每个data-name形式的属性都会有一个对应的属性，只不过属性名没有data-前缀。

##### 11.3.6 插入标记

- 使用插入标记的技术，直接插入HTML字符串更简单，速度也更快。

###### 1. innerHTML属性

- 在读模式下，InnerHTML属性返回与调用元素的所有子节点（包括元素、注释和文本节点）对应的HTML标记。
- 在写模式下，innerHTML会根据指定的值创建新的DOM树，然后用这个DOM树完全替换调用元素原先的所有子节点。
- 设置了innerHTML之后，可以像访问文档中的其他节点一样访问新创建的节点。
- 使用innerHTML属性不能插入\<script\>、\<style\>这类无作用域的元素，也就是在页面中看不到的元素。
- 并不是所有元素都支持innerHTML属性。不支持innerHTML的元素有：\<col\>、\<colgroup\>、\<frameset\>、\<head\>、\<html\>、\<style\>、\<table\>、\<tbody\>、\<thead\>、\<tfoot\>和\<tr\>。

###### 2. outerHTML属性

- 在读模式下，outerHTML返回调用它的元素及所有子节点的HTML标签。
- 在写模式下，outerHTML会根据指定HTML字符串创建新的DOM子树，然后用这个DOM子树完全替换调用元素。

###### 3. insertAdjacentHTML()方法

- 这个方法接收两个参数：插入位置和要插入的HTML文本。第一个参数必须是下列值之一：
  - "beforebegin"，在当前元素之前插入一个紧邻的同辈元素；
  - "afterbegin"，在当前元素值下插入一个新的子元素或在第一个子元素之前再插入新的子元素；
  - "beforeend"，在当前元素之下插入一个新的子元素或在最后一个子元素之后再插入新的子元素；
  - "afterend"，在当前元素之后插入一个紧邻的同辈元素。
- 第二个参数是一个HTML字符串（与innerHTML和outerHTML的值相同），如果浏览器无法解析该字符串，就会抛出错误。

###### 4. 内存与性能问题

- 使用本节介绍的方法替换子节点可能会导致浏览器的内存占用问题，尤其在IE中，问题更加明显。在删除带有时间处理程序或引用了其他JavaScript对象子树时，就有可能导致内存占用问题。
- 在使用innerHTML、outerHTML属性和insertAdjacentHTML()方法时，最好先手工删除要被替换的元素的所有时间处理程序和JavaScript对象属性。

##### 11.3.7 scrollIntoView()方法

- scrollIntoView()方法可以在所有HTML元素上调用，通过滚动浏览器窗口或某个容器元素，调用元素就可以出现在视口中。如果给这个方法传入true作为参数，或者不传入任何参数，那么窗口滚动之后会让调用元素的顶部与视口顶部尽可能平齐。如果传入false作为参数，调用元素会进可能 全部出现在视口中，（可能的话，调用元素的地步会与视口顶部平齐）不过顶部不一定平齐。

#### 11.4 专有扩展

虽然所有浏览器开发商都知晓坚持标准的重要性，但在发现某项功能缺失时，这些开发商都会一如既往地向DOM中添加专有扩展，以弥补功能上的不足。表面上看，这种各行其事的做法似乎不太好，但实际上专有扩展为web开发领域提供了很多重要的功能，这些功能最终都在HTML5规范中得到了标准化。

##### 11.4.1 文档模式

- 文档模式(document mode) 决定了可以使用什么功能。总共有一下4种文档模式。
  - IE5：以混杂模式渲染页面（IE5的默认模式就是混杂模式）。IE8及更高版本中的新功能都无法使用。
  - IE7：以IE7标准模式渲染页面。IE8及更高版本中的新功能都无法使用。
  - IE8：以IE8标准模式渲染页面。IE8中的新功能都可以使用，因此可以使用Selectors API、更多CSS2级选择符和某些CSS3功能，还有一些HTML5的功能。不过IE9中的新功能无法使用。
  - IE9：以IE9标准模式渲染页面。IE9中的新功能都可以使用，比如ECMAScript5、完整的CSS3以及更多HTML5功能。这个文档模式是最高级的模式。
- 通过document.documentMode属性可以只知道给定页面使用的是什么文档模式。

##### 11.4.2 children属性

- children属性是HTMLCollection的实例，只包含元素中童颜还是元素的子节点。除此之外，children属性和childNodes没有什么区别，即在元素只包含元素子节点时，这两个属性的值相同。

##### 11.4.3 contains()方法

- contains()方法可以确定某个节点是不是另一个节点的后代。调用contains()方法的应该是祖先节点，也就是搜索开始的节点，这个方法接收一个参数，即要检测的后代节点。如果被检测的节点是后代节点，该方法返回true，否则返回false。

##### 11.4.4 插入文本

###### 1. innerText属性

通过innerText属性可以操作元素中包含的所有文本内容，包括子文档树中的文本。在通过innerText读取值时，它会按照由浅入深的顺序，将子文档树中的所有文本拼接起来。在通过innerText写入值时，结果会删除元素的所有子节点，插人包含相应文本值的文本节点。

###### 2. outerText属性

除了作用范围扩大到了包含调用它的节点之外，outerText与innerText基本上没有多大区别。在读取文本值时，outerText与innerText的结果完全一样，但在写模式下，outerText就完全不同了：outerText不只是替换调用它的元素的子节点，而是会替换整个元素（包括子节点）。

##### 11.4.5 滚动

- scrollIntoViewIfNeeded(alignCenter)：值在当前元素在视口中部可见的情况下，才滚动浏览器窗口或容器元素，最终让它可见。如果当前元素在视口中可见，这个方法说明也不做。如果将可选的alignCenter参数设置为true，则表示尽量将元素显示在视口中部（垂直方向）。Safari和Chrome实现了这个方法。
- ScrollByLines(lineCount)：将元素的内容滚动指定的行高，lineCount值可以是正值，也可以是负值。Safari和Chrome实现了这个方法。
- scrollByPages(pageCount)：将元素的内容滚动指定的页面高度，具体高度由元素的高度决定。Safari和Chrome实现了这个方法。