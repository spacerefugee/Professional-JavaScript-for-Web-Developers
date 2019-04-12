### 第12章 DOM2和DOM3

DOM1级主要定义的是HTML和XML文档的底层结构。DOM2和DOM3级则在这个结构的基础上引入了更多的交互能力，也支持了更高级的XML特性。为此，DOM2和DOM3级分为许多模块（模块之间具有某种关联），分别描述了DOM的某个非常具体的子集。这些模块如下：

- DOM2级核心(DOM Level 2 Core)：在1级核心基础上构建，为节点添加了更多方法和属性。
- DOM2级试图(DOM Level 2 Views)：为文档定义了基于样式信息的不同视图。
- DOM2级事件(DOM Level 2 Events)：说明了如何使用事件与DOM文档交互。
- DOM2级样式(DOM Level 2 Style)：定义了如何以变成方式来访问和改变CSS样式信息。
- DOM2级遍历和范围(DOM Level 2 Traversal and Range)：引入了遍历DOM文档和选择其特定部分的新街口。
- DOM2级HTML(DOM Level 2 HTML)：在1级HTML基础上构建，添加了更多属性、方法和新接口。

#### 12.1 DOM变化

DOM2级和3级的目的在于扩展DOM API，以满足操作XML的所有需求，同时提供更好的错误处理及特性检测能力。从某种意义上讲，实现这一目的很大程度意味着对命名空间的支持。“DOM2级核心”没有引入新类型，它只是在DOM1级的基础上通过增加新方法和新属性来增强了既有类型。“DOM3级核心”同样增强了既有类型，但也引人了一些新类型。
类似地，“DOM2级视图”和“DOM2级HTML”模块也增强了DOM接口，提供了新的属性和方法。由于这两个模块很小，因此我们将把它们与"DOM2级核心”放在一起，讨论基本JavaScript对象的变化。可以通过下列代码来确定浏览器是否支持这些DOM模块。

```javascript
var supportsDOM2Core = document.implementation.hasFeature("Core", "2.0");
var supportsDOM3Core = document.implementation.hasFeature("Core", "3.0");
var supportsDOM2HTML = document.implementation.hasFeature("HTML", "2.0");
var supportsDOM2Views = document.implementation.hasFeature("Views", "2.0");
var supportsDOM2XML = document.implementation.hasFeature("XML", "2.0");
```

##### 12.1.1 针对XML命名空间的变化

- 有了XML命名空间，不同XML文档的 元素就可以混合在一起，共同构成格式良好的文档，而不必担心发生命名冲突。从技术上说，HTML不支持XML命名空间，但XHTML支持XML命名空间。
- 命名空间要使用xmlns特性来指定。XHTML的命名空间是http://www.w3.org/1999/xhtml ，在任何格式良好XHTML页面中，都应该将其包含在\<html\>元素中。如下例子所示。

```html
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Example XHTML page</title>
    </head>
    <body>
        Hello world!
    </body>
</html>
```

对这个例子而言，其中的所有元素默认都被视为XHTML命名空间中的元素。要想明确地为XML命名空间创建前缀，可以使用xmlns后跟冒号，再后跟前缀，如下所示。

```html
<xhtml:html xmlns:xhtml="http://www.w3.org/1999/xhtml">
	<xhtml:head>
        <xhtml:title>Example XHTML page</xhtml:title>
    </xhtml:head>
    <xhtml:body>
    	Hello world!
    </xhtml:body>
</xhtml:html>
```

这里为XHTML的命名空间定义了一个名为xhtml的前缀，并要求所有XHTML元素都以该前缀开头。有时候为了避免不同语言间的冲突，也需要使用命名空间来限定特性，如下例子所示。

```html
<xhtml:html xmlns:xhtml="http://www.w3.org/1999/xhtml">
	<xhtml:head>
        <xhtml:title>Example XHTML page</xhtml:title>
    </xhtml:head>
    <xhtml:body xhtml:class="home">
    	Hello world!
    </xhtml:body>
</xhtml:html>
```

在这个例子中的特性class带有一个xhtml前缀。在只基于一种语言编写XML文档的情况下，命名空间实际上也没有什么用。不过，在混合使用两种语言的情况下，命名空间的用处就非常大了。

###### 1. Node类型的变化

在DOM2级中，Node类型包含下列特定于明明空间的属性。

- localName：不带命名空间前缀的节点名称。
- namespaceURI：命名空间URI或者（在未指定的情况下是）null。
- prefix：命名空间前缀或者（在未指定的情况下是）null。

DOM3级在此基础上更进一步，又引入了下列与命名空间有关的方法。

- isDefaultNamespace(namespaceURI)：在指定的namespaceURI是当前节点的默认命名空间的情况下返回true。
- lookupNamespaceURI(prefix)：返回给定prefix的命名空间。
- lookupPrefix(namespaceURI)：返回给定namespaceURI的前缀。

###### 2. Document类型的变化

DOM2级中的Document类型也法神过来变化，包含了下列与命名空间有关的方法。

- createElementNS(namespaceURI, tagName)：使用给定的tagName创建一个属于命名空间namespaceURI的新元素。
- createAttributeNS(namespaceURI, attributeName)：使用给定的attributeName创建一个属于命名空间namespaceURI的新特性。
- getElementsByTagNameNS(namespaceURI, tagName)：返回属于命名空间namespaceURI的tagName元素的NodeList。

###### 3. ELement类型的变化

“DOM2级核心”中有关Element的变化，主要涉及操作特性。新增的方法如下。

- getAttributeNS(namespaceURI, localName)：取得属于命名空间namespaceURI且名为localName的特性。
- getAttributeNodeNS(namespaceURI, localName)：取得属于命名空间namespaceURI且名为localName的特性节点。
- getElementsByTagNameNS(namespaceURI, tagName)：返回属于命名空间namespaceURI的tagName元素的NodeList。
- hasAttributeNS(namespaceURI, localName)：确定当前元素是否有一个名为localName的特性，而且该特性的命名空间是namespaceURI。注意，“DOM2级核心”也增加了一个hasAttribute()方法，用于不考虑命名空间的情况。
- removeAttributeNS(namespaceURI, localName)：删除属于命名空间namespaceURI切名为localName的特性。
- setAttributeNS(namespaceURI, qualifiedName, value)：设置属于命名空间namespaceURI且名为qualifiedName的特性的值为value。
- setAttributeNodeNS(attNode)：设置属于命名空间namespaceURI的特性节点。

###### 4. NamedNodeMap类型的变化

NamedNodeMap类型也新增了下列与命名空间有关的方法。由于特性时通过NamedNodeMap表示的，因此这些方法多数情况下只针对特性使用。

- getNamedItemNS(namespaceURI, localName)：取得属于命名空间namespaceURI且名为localName的项。
- removeNamedItemNS(namespaceURI, localName)：移除属于命名空间namespaceURI且名为localName的项。
- setNamedItemNS(node)：添加node，这个节点已经事先指定了命名空间信息。

##### 12.1.2 其他方面的变化

DOM的其他部分在“DOM2级核心”中也发生了一些变化。这些变化与XML命名空间无关，而是更倾向于确保API的可靠性及完整性。

###### 1. DocumentType类型的变化

DocumentType类型新增了3个属性：publicId、systemId和internalSubset。其中前两个属性表示的是文档类型声明中的两个信息段，这两个信息段在DOM1级中使没有办法访问到的。最后一个属性internalSubset，用于访问包含在文档类型声明中的额外定义。

###### 2. Document类型的变化

Document类型的变化中唯一与命名空间无关的方法是importNode()。这个方法的用途是从一个文档中取得一个节点，然后将其导入到另一个文档，使其成为这个文档结构的一部分。

###### 3. Node类型的变化

Node类型中唯一与命名空间无关的变化就是添加了isSupported()。该方法用于确定当前节点具有什么能力。

###### 4. 框架的变化

框架和内嵌框架分别用HTMLFrameElement和HTMLIFrameElement表示，他们在DOM2级中都有了一个新属性，contentDocument。这个属性包含一个指针，指向表示框架内容的文档对象。

#### 12.2 样式

在HTML中定义样式的方式有3种：通过\<link/\>元素包含外部样式表文件、使用\<style\>元素定义嵌入式样式，以及使用style特性定义针对特定元素的样式。“DOM2级样式”模块围绕这3种应用样式的机制提供了一套API。要确定浏览器是否支持DOM2级定义的CSS能力，可以使用下列代码。

```javascript
var supportsDOM2CSS = document.implementation.hasFeature("CSS", "2.0");
var supportsDOM2CSS2 = document.implementation.hasFeature("CSS2", "2.0");
```

##### 12.2.1 访问元素的样式

任何支持style特性的HTML元素在JavaScript中都有一个对应的style属性。这个style对象时CSSStyleDeclaration的实例，包含着通过HTML的style特性指定的所有样式信息，但不包含于外部样式表或嵌入样式表经层叠而来的样式。在style特性中指定的任何CSS属性都将表现为这个style对象的相应属性。对于使用短划线的CSS属性名，必须将其转换成驼峰大小写形式，才能通过JavaScript来访问。下表列出几个常见的CSS属性及其在style对象中对应的属性名。

CSS属性 | JavaScript属性
:-: | :-:
background-image|style.backgroundImage
color|style.color
display|style.display
font-family|style.fontFamily

多数情况下，都可以通过简单的地转换属性名的格式来实现转换。其中一个不能直接转换的CSS属性就是float。由于float是JavaScript中的保留字，因此不能用作属性名。

###### 1. DOM样式属性和方法

"DOM2级样式"规范还为style对象定义了一些属性和方法。这些属性和方法在提供元素的style特性值的同时，也可以修改样式。下面列出了这些属性和方法。

- cssText：如前所述，通过它能够访问到style特性中的CSS代码。
- length：应用给元素的CSS属性的数量。
- parentRule：表示CSS信息的CSSRule对象。
- getPropertyCSSValue(propertyName)：返回包含给定属性值的CSSValue对象。
- getPropertyPriority(propertyName)：如果给定的属性是用了!important设置，则返回"important"；否则，返回空字符串。
- getPropertyValue(propertyName)：返回给定属性的字符串值。
- item(index)：返回给定位置的CSS属性的名称。
- removeProperty(propertyName)：从样式中删除给定属性。
- setProperty(propertyName, value, priority)：将给定属性设置为相应的值，并加上优先权标志（"important"或者一个空字符串）。

###### 2. 计算的样式

虽然style对象能够提供支持style特性的任何元素的样式信息，但它不包含那些从其他样式表层叠而来并影响到当前元素的样式信息。“DOM2级样式”增强了document.defaultView，提供了getComputedStyle()方法。这个方法接收两个参数：要取得计算样式的元素和一个伪元素字符串（例如“:after”）。如果不需要伪元素信息，第二个参数可以是null。getComputedStyle()方法返回一个CSSStyleDeclaration对象（与style属性的类型相同），其中包含当前元素的所有计算的样式。

##### 12.2.2 操作样式表

CSSStyleSheet类型表示的是样式表，包括通过\<link\>元素包含的样式表和在\<style\>元素中定义的样式表。CSSStyleSheet对象时一套只读的接口。使用下面的代码可以确定浏览器是否支持DOM2级样式表。

```javascript
var supportsDOM2StyleSheets = document.implementation.hasFeature("StyleSheets", "2.0");
```

CSSStyleSheet继承自StyleSheet，后者可以作为一个基础接口来定义非CSS样式表。从StyleSheet接口继承而来的属性如下：

- disabled：表示样式表是否被禁用的布尔值。这个属性是可读写的，将这个值设置为true可以禁用样式表。
- href：如果样式表是通过\<link\>包含的，则是样式表的URL；否则，是null。
- media：当前样式表支持的所有媒体类型的集合。与所有DOM集合一样，这个集合也有一个length属性和一个item()方法。也可以使用方括号语法取得集合中特定的项。如果集合是空的列表，表示样式表适用于所有媒体。在IE中，media是一个反映\<link\>和\<style\>元素media特性值的字符串。
- ownerNode：指向用用当前样式表的节点的指针，样式表可能是在HTML中通过\<link\>或\<style\>引入的（在XML中可能是通过处理器指令引入的）。如果当前样式表是其他样式表通过@import导入的，则这个属性值为null。IE不支持这个属性。
- parentStyleSheet：在当前样式表是通过@import导入的情况下，这个属性是一个指向导入它的样式表的指针。
- title：ownerNode中title属性的值。
- type：表示样式表类型的字符串。对CSS样式表而言，这个字符串时"type/css"。

除了disabled属性之外，其他属性都是制度的。在支持以上所有这些属性的基础上，CSSStyleSheet类型还支持下列属性和方法：

- cssRules：样式表中包含的样式规则的集合。IE不支持这个属性，但有一个类型的rules属性。
- ownerRule：如果样式表是通过@import导入的，这个属性就是一个指针，指向表示导入的规则；否则，值为null。IE不支持这个属性。
- deleteRule(index)：删除cssRules集合中指定位置的规则。IE不支持这个方法，但支持一个类似的removeRule()方法。
- insertRule(rule, index)：向cssRules集合中指定位置插入rule字符串。IE不支持这个方法，但直至一个类似的addRule()方法。

应用于文档的所有样式表是通过document.styleSheets集合来表示的。通过这个集合的length属性可以获知文档中样式表的数量，而通过方括号语法或item()方法可以访问每一个样式表。

###### 1. CSS规则

CSSRule对象表示样式表中的每一条规则。实际上，CSSRule是一个供其他多种类型继承的基类型，其中最常见的就是CSSStyleRule类型，表示样式信息（其他规则还有@import、@font-face、@page和@charset，但这些规则很少有必要通过脚本来访问）。CSSStyleRule对象包含下列属性。

- cssText：返回整条规则对应的文本。由于浏览器对样式表的内部处理方式不同，返回的文本可能会与样式表中实际的文本不一样；Safari始终都会将文本转换成全部小写。IE不支持这个属性。
- parentRule：如果当前规则是导入的规则，这个属性引用的就是导入规则；否则，这个值为null。IE不支持这个属性。
- parentStyleSheet：当前规则所属的样式表。IE不支持这个属性。
- selectorText：返回当前规则的选择符文本。由于浏览器对样式表的内部处理方式不同，返回的文本可能会与样式表中实际的文本不一样。在Firefox、Safari、Chrome和IE中这个属性是只读的。Opera允许修改。
- style：一个CSSStyleDeclaration对象，可以通过它设置和取得规则中特定的样式值。
- type：表示规则类型的常量值。对于样式规则，这个值是1。IE不支持这个属性。

###### 2. 创建规则

DOM规定，要向现有样式表中添加新规则，需要使用insertRule()方法。这个方法接收两个参数：规则文本和表示在哪里插入规则的索引。

###### 3. 删除规则

从样式表中删除规则的方法是deleteRule()，这个方法接收一个参数：要删除的规则的位置。

##### 12.2.3 元素大小

DOM中没有规定如何确定页面中元素的大小。IE为此率先引入了一些属性，一边开发人员使用。目前，所有主要的浏览器都已经支持这些属性。

###### 1. 偏移量

偏移量(offset dimension)包括元素在屏幕上占用的所有可见的空间。元素的可见大小由其高度、宽度决定，包括所有内边距、滚动条和边框大小。通过下列4个属性可以取得元素的偏移量。

- offsetHeight：元素在垂直方向上占用的空间大小，以像素计。包括元素的高度、（可见的）水平滚动条的高度、上边框高度和下边框高度。
- offsetWidth：元素在水平方向上占用的空间大小，以像素计。包括元素的宽度、（可见的）垂直滚动条宽度、左边框宽度和右边框宽度。
- offsetLeft：元素的左外边框至包含元素的左内边框之间的像素距离。
- offsetTop：元素的上边框至包含元素的上内边框之间的像素距离。

其中，offsetLeft和offsetTop属性与包含元素有关，包含元素的引用保存在offsetParent属性中。offsetParent属性不一定与offsetNode的值相等。

###### 2. 客户区大小

元素的客户区大小(client dimension)，指的是元素内容及其内边距所占据的空间大小。有关客户区大小的属性有两个：clientWidth和clientHeight。其中，clientWidth属性是元素内容区款速加上左右内边距宽度；clientHeight属性是元素内容区高度加上上下内边距高度。

###### 3. 滚动大小

滚动大小(scroll dimension)，指的是包含滚动内容的元素的大小。有些元素即使没有执行任何代码也能自动地添加滚动条；但另外一些元素，则需要通过CSS的overflow属性进行设置才能滚动。以下是4个与滚动大小相关的属性。

- scrollHeight：在没有滚动条的情况下，元素内容的总高度。
- scrollWidth：在没有滚动条的情况下，元素内容的总宽度。
- scrollLeft：被因此在内容区域左侧的像素数。通过设置这个属性可以改变元素的滚动位置。
- scrollTop：被因此在内容区域上方的像素数。通过设置这个属性可以改变元素的滚动位置。

###### 4. 确定元素大小

IE、Firefox 3+、Safari 4+、Opera 9.5及Chrome为每个元素都提供了一个getBoundingClientRect()方法。这个方法返回一个矩形对象，包含4个属性：left、top、right和bottom。这些属性给出了元素在页面中相对视口的位置。

```javascript
function getBoundingClientRect(element) {
	var scrollTop = document.documentElement.scrollTop;
	var scrollLeft = document.documentElement.scrollLeft;

	if (element.getBoundingClientRect) {
		if (typeof arguments.callee.offset != "number") {
			var temp = document.createElement("div");
			temp.style.cssText = "position:absolute;left:0;top:0;";
			document.body.appendChild(temp);
			arguments.callee.offset = -temp.getBoundingClientRect().top - scrollTop;
			document.body.removeChild(temp);
			temp = null;
		}

		var rect = element.getBoundingClientRect();
		var offset = arguments.callee.offset;

		return {
			left: rect.left + offset,
			right: rect.right + offset,
			top: rect.top + offset,
			bottom: rect.bottom + offset
		};
	} else {

		var actualLeft = getElementLeft(element);
		var actualTop = getElementTop(element);

		return {
			left: actualLeft - scrollLeft,
			right: actualLeft + element.offsetWidth - scrollLeft,
			top: actualTop - scrollTop,
			bottom: actualTop + element.offsetHeight - scrollTop
		};
	}
}
```

这个函数在getBoundingClientRect()有效时，就使用这个原生方法，而在这个方法无效时，则使用默认的计算公式。在某些情况下，这个函数返回的值可能会有所不同，例如使用表格布局或使用滚动元素的情况下。

#### 12.3 遍历

“DOM2级遍历和范围”模块定义了两个用于辅助完成顺序遍历DOM结构的类型：NodeIterator和TreeWalker。这两个类型能够基于给定的起点对DOM结构执行深度优先(depth-first)的遍历操作。使用下列代码可以检测浏览器对DOM2级遍历能力的支持情况。

```javascript
var supportsTraversals = document.implementation.hasFeature("Traversal", "2.0");
var supportsNodeIterator = (typeof document.createNodeIterator == "function");
var supportsTreeWalker = (typeof document.createTreeWalker == "function");
```

##### 12.3.1 NodeIterator

NodeIterator类型是两者中比较简单的一个，可以使用document.createNodeIterator()方法创建它的新实例。这个方法接受下列4个参数。

- root：想要作为搜素起点的树中的节点。
- whatToShow：表示要访问那些节点的数字代码。
- filter：是一个NodeFilter对象，或者一个表示应该接受还是拒绝某种特定节点的函数。
- EntityReferenceExpansion：布尔值，表示是否要扩展实体引用。这个参数在HTML页面中没有，因为其中的实体引用不能扩展。

whatToShow参数是一个位掩码，通过应用一或多个过滤器（filter）来确定要访问哪些节点。这个参数的值以常量形式在NodeFilter类型中定义，如下所示。

- NodeFilter.SHOW_ALL：显示所有类型的节点。
- NodeFilter.SHOW_ELEMENT：显示元素节点。
- NodeFilter.SHOW_ATTRIBUTE：显示特性素节点。由于DOM结构原因，实际上不能使用这个值。
- NodeFilter.SHOW_TEXT：显示文本节点。
- NodeFilter.SHOW_CDATA_SECTION：显示CDATA节点。对HTML页面没有用。
- NodeFilter.SHOW_ENTITY_REFERENCE：显示实体引用节点。对HTN化页面没有用。
- NodeFilter.SHOW_ENTITYE：显示实体节点。对HTML页面没有用。
- NodeFilter.SHOW_PROCESSING_INSTRUCTION：显示处理指令节点。对HTML页面没有用。
- NodeFilter.SHOW_COMMENT：显示注释节点。
- NodeFilter.SHOW_DOCUMENT：显示文档节点。
- NodeFilter.SHOW_DOCUMENT_TYPE：显示文档类型节点。
- NodeFilter.SHOW_DOCUMENT_FRAGMENQ：显示文档片段节点。对HTML页面没有用。
- NodeFilter.SHOW_NOTATION：显示符号节点。对HTML页面没有用。
  除了NodeFilter.SHOW_ALL之外，可以使用按位或操作符来组合多个选项，如下面的例子所示：

```javascript
var whatToShow = NodeFilter.SHOW_ELEMENT | NodeFilter.SHOW_TEXT;
```

可以通过createNodeIterator()方法的filter参数来指定自定义的NodeFilter对象，或者指定一个功能类似节点过滤器(node filter)的函数。每个NodeFilter对象只有一个方法，即acceptNode()；如果应该访问给定的节点，该方法返回NodeFilter.FILTER_ACCEPT，如果不应该访问给定的节点，该方法返回NodeFilter.FILTER_SKIP。由于NodeFilter是一个抽象的类型，因此不能直接创建它的实例。在必要时，只要创建一个包含acceptNode()方法的对象，然后将这个对象传入createNodeIterator()中即可。

第三个参数也可以是一个与acceptNode()方法类似的函数。

NodeIterator类型的两个主要方法是nextNode()和previousNode()。顾名思义，在深度优先的DOM子树遍历中，nextNode()方法用于向前前进一步，而previousNode()用于向后后退一步。

##### 12.3.2 TreeWalker

TreeWalker是NodeIterator的一个更高级的版本。除了包括nextNode()和previousNode()在内的相同功能之外，这个类型还提供了下列用于在不同方向上遍历DOM结构的方法。

- parentNode()：遍历到当前节点的父节点。
- firstChild()：遍历到当前节点的第一个个子节点。
- lastChild()：遍历到当前节点的最后一个子节点。
- nextSibling()：遍历到当前节点的下一个同辈节点。
- previousSibling()：遍历到当前节点的上一个同辈节点。

创建Treewalker对象要使用document.createTreeWalker()方法，这个方法接收4个参数与document.createNodeIterator()方法相同：作为遍历起点的根节点、要显示的节点类型、过滤器和一个表示是否扩展实体引用的布尔值。

#### 12.4 范围

为了让开发人员更方便地控制页面，“DOM2级遍历和范围”模块定义了“范围“(mnge）接口。通过范围可以选择文档中的一个区域，而不必考虑节点的界限（选择在后台完成，对用户是不可见的）。在常规的DOM操作不能更有效地修改文档时，使用范围往往可以达到目的。Ftrefox、Opera、Safari和Chrome都支持DOM范围。化以专有方式实现了自己的范围特性。

##### 12.4.1 DOM中的范围

DOM2级在Document类型中定义了createRange()方法。在兼容DOM的浏览器中，这个方法属于document对象。使用hasFeature()或者直接检测该方法，都可以确定浏览器是否支持范围。

```javascript
var supportsRange = document.implementation.hasFeature("Range", "2.0");
var alsoSupportsRange = (typeof document.createRange = "function");
```

每个范围由一个Range类型的实例标识，这个实例拥有很多属性和方法。下列属性提供了当前范围在文档中的位置信息。

- startContainer：包含范围起点的节点（即选区中第一个节点的父节点）。
- startOffset：范围在startContainer中起点的偏移量。如果startContainer是文本节点、注释节点或CDATA节点，那么startOffset就是范围起点之前跳过的字符数量。否则，startOffset就是范围中第一个子节点的索引。
- endContainer：包含范围重点的节点（即选区中最后一个节点的父节点）。
- endOffset：范围在endContainer中重点的偏移量（与startOffset遵循相同的取值规则）。
- commonAncestorContainer：startContainer和endContainer共同的祖先节点在文档树中位置最深的那个。

###### 1. 用DOM范围实现简单选择

要使用范围来选择文档中的一部分，最简单的方式就是使用selectNode()或selectNodeContents()。这两个方法都接收一个参数，即一个DOM节点，然后使用该节点中的信息来填充范围。其中，selectNode()方法选择整个节点，包括其子节点；而selectNodeContents()方法则只选择节点的子节点。

为了更精细地控制将哪些节点包含在范围中，还可以使用下列方法。

- setStartBefore(refNode)：将范围的起点设置在refNode之前，因此refNode也就是范围选区中的第一个子节点。同时会将startContainer属性设置为refNode.parentNode，将startOffset属性设置为refNode在其父节点的childNodes集合中的索引。
- setStartAfter(refNode)：将范围的起点设置在refNode之后，因此refNode也就不再范围之内了，其下一个同辈节点才是范围选区中的第一个子节点。同时会将startContainer属性设置为refNode.parentNode，将startOffset属性设置为refNode在其父节点的childNodes集合中的索引加1。
- setEndBofore(refNode)：将范围的重点设置在refNode之前，因此refNode也就不再范围之内了，其上一个同辈节点才是范围选区中的最后一个子节点。同时会将endContainer属性设置为refNode.parentNode，将endOffset属性设置为refNode在其腹肌诶单的childNodes集合中的索引。
- setEndAfter(refNode)：将范围的重点设置在refNode之后，因此refNode也就是范围选区中的最后一个子节点。同时会将endContainer属性设置为refNode.parentNode，将endOffset属性设置为refNode在其父节点的childNodes集合中的所以加1。

###### 2. 用DOM范围实现复杂选择

要创建复杂的范围就的是用setStart()和setEnd()方法。这两个方法都接收两个参数：一个参照节点和一个偏移量值。对setStart()来说，参照节点会变成startContainer，而偏移量会变成startOffset。对于setEnd()来说，参照节点会变成endContainer，而偏移量会变成endOffset。

###### 3. 操作DOM范围中的内容

在创建范围时，内部会为这个范围创建一个文档片段，范围所属的全部节点都被添加到了这个文档片段中。为了创建这个文档片段，范围内容的格式必须正确有效。在前面的例子中，我们创建的选区分别开始和结束于两个文本节点的内部，因此不能算是格式良好的DOM结构，也就无法通过DOM来表示。但是，范围知道自身缺少哪些开标签和闭标签，它能够重新构建有效的DOM结构一边我们对其进行操作。

- deleteContents()方法能够从文档中删除范围所包含的内容。
- extractContents()方法会从文档中移除范围选区，并返回范围的文档片段。
- cloneContents()方法可以创建范围对象的一个副本，然后在文档的其他地方插入该副本。

###### 4. 插入DOM范围中的内容

- insertNode()方法可以向范围选区的开始处插入一个节点。
- surroundContents()方法可以环绕范围插入内容。

###### 5. 折叠DOM范围

所谓折叠范围，就是指范围中未选择文档的任何部分。可以用文本框来描述折叠范围的过程。假设文本框中有一行文本，用鼠标选择了其中一个完整的单词。然后单击鼠标左键，选区小时，而光标则落在了其中的两个字母之间。同样，在折叠范围时，其位置会落在文档中的两个部分之间，可能是范围选区的开始位置，也可能是结束位置。

- collapse()方法可以折叠范围。这个方法接收一个参数，一个布尔值，表示要折叠刀范围的哪一端。true表示折叠到范围的起点，false表示折叠刀范围的重点。
- collapsed属性可以确定范围是否已经折叠完毕。

###### 6. 比较DOM范围

在有多个范围的情况下，可以使用compareBoundaryPoints()方法来确定这些范围是否有公共的边界（起点或终点）。这个方法接收两个参数：表示比较方式的常量值和要比较的范围。表示比较方法的常量值如下所示：

- Range.START_TO_START(0)：比较第一个范围和第二个范围的起点；
- Range.START_TO_END(1)：比较第一个范围的起点和第二个范围的终点；
- Range.END_TO_END(2)：比较第一个范围和第二个范围的终点；
- Range.END_TO_START(3)：比较第一个范围的终点和第一个范围的起点。

compareBoundaryPoints()方法可能返回值如下：

- 如果第一个范围中的点位于第二个范围中的点之前，返回-1；
- 如果两个点相等，返回0；
- 如果第一个范围中的点位于第二个范围中的点之后，返回1。

###### 7. 复制DOM范围

可以使用cloneRange()方法复制范围。这个方法会创建调用它的范围的一个副本。

###### 8. 清理DOM范围

在使用完范围之后，最好是调用detach()方法，一边从创建范围的文档中分离出该范围。调用detach()之后，就可以放心地接触对范围的引用，从而让垃圾回收机制回收其内存了。

##### 12.4.2 IE8及更早版本中的范围

虽然IE9支持DOM范围，但IE8及之前版本不支持DOM范围。不过，IE8及早期版本支持一种类似的概念，即文本范围（text range）。文本范围时IE专有的特性，其他浏览器都不支持。文本范围处理的主要是文本（不一定是DOM节点）。通过\<body\>、\<button\>、\<input\>和\<textarea\>等这几个元素，可以调用createTextRange()方法来创建文本范围。