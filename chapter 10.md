### 第10章 DOM
- DOM(文档对象模型)是针对HTML和XML文档的一个API（应用程序编程接口）。DOM秒回了一个层次化的节点树，允许开发人员同添加、移除和修改页面的某一部分。DOM脱胎于Netscape及微软公司创始的DHTML（动态HTML），但现在它已经成为表现和操作页面标记的真正的跨平台、语言中立的方式。

#### 10.1 节点层次
- DOM可以将任何HTML或XML文档描绘成一个由多层节点构成的结构。节点分为几种不同的类型，每种类型分别表示文档中不同的信息及（或）标记。每个节点都拥有各自的特点、数据和方法，另外也与其他节点存在某种关系。节点之间的关系构成了层次，而所有页面标记则表现为一个以特定节点为根节点的树形结构。
- 每一段标记都可以通过树中的一个节点来表示：HTML元素通过元素节点表示，特性(attribute)通过特性节点表示，文档类型通过文档类型节点表示，而注释则通过注释节点表示。总共有12中节点类型，而这些类型都继承自一个基类型。

##### 10.1.1 Node类型
- DOM1级定义了一个Node接口，该接口将由DOM中的所有节点类型实现。这个Node接口在JavaScript中使作为Node类型实现的；除了IE之外，在其他所有浏览器中都可以访问到这个类型。JavaScript中的所有节点类型都继承自Node类型，因此所有节点类型都共享者相同的基本属性和方法。
- 每个节点都有一个nodeType属性，用于标明节点的类型。节点类型由在Node类型中定义的下列12个数值常量来表示，任何节点类型必居其一：
  - Node.ELEMENT\_NODE(1);
  - Node.ATTRIBUTE\_NODE(2);
  - Node.TEXT\_NODE(3);
  - Node.CDATA\_SECTION\_NODE(4);
  - Node.ENTITY\_REFERENCE\_NODE(5);
  - Node.ENTITY\_NODE(6);
  - Node.PROCESSING\_INSTRUCTION\_NODE(7);
  - Node.COMMENT\_NODE(8);
  - Node.DOCUMENT_NODE(9);
  - Node.DOCUMENT\_TYPE\_NODE(10);
  - Node.DOCUMENT\_FRAGMENT\_NODE(11);
  - Node.NOTATION\_NODE(12);
- 并不是所有节点类型都是收到Web浏览器的支持。开发人员最常用的就是元素和文本节点。

###### 1. nodeName和nodeValue属性
- 这两个属性的值完全取决于节点的类型，在使用者两个值之前，最好是像下面这样先检测一下节点的类型。

```javascript
if (someNode.nodeType == 1) {
    value = someNode.nodeName; // nodeName的值是元素的标签名
}
```
- 在这个例子中，首先检查节点类型，看它是不是一个元素。如果是，则取得并保存nodeName的值。对于元素节点，nodeName中保存的始终都是元素的标签名，而nodeValue的值始终为null。

###### 2. 节点关系
- 文档中所有的节点之间都存在这样或那样的关系。节点间的各种关系可以用传统的家族关系来描述，相当于把文档树比喻成家谱。在HTML中，可以将\<body\>元素看成是\<html\>元素的子元素；相应地，也就可以将\<html\>元素看成是\<body\>元素的父元素。而\<head\>元素，则可以看成是\<body\>元素的同胞元素，因为他们都是同一个父元素\<html\>的直接子元素。
- 每个节点都有一个childNodes属性，其中保存着一个NodeList对象。NodeList是一种类数组对象，用于保存一组有序的节点，可以通过位置来访问这些节点。请注意，虽然可以通过方括号语法来访问NodeList的值，而且这个对象也有length属性，但它并不是Array的实例。NodeList对象的独特之处在于，它实际上是基于DOM结构动态执行查询的结果，因此DOM结构的变化能够自动反映在NodeList对象中。我们常说，NodeList是有生命、有呼吸的对象，而不是在我们第一次访问它们的某个瞬间拍摄下来的一张快照。
- 可以通过方括号也可以使用item()方法来访问保存在NodeList中的节点。
- 每个节点都有一个parentNode属性，该属性指向文档树中的父节点。包含在childNodes列表中的所有节点都具有相同的父节点，因此他们的parentNode属性都指向同一个节点。 此外，包含在childNodes列表中的每个节点相互之间都是同胞节点。通过使用列表中每个节点的previousSibling和nextSibling属性，可以访问同一列表中的其他节点。列表中的第一个节点的previousSibling属性值为null，而列表中最后一个节点的nextSibling属性的值同样也为null。

###### 3. 操作节点
- 因为关系指针都是只读的，所以DOM提供了一些操作节点的方法。其中，最常用的方法是appendChild()，用于向childNodes列表的末尾添加一个节点。添加节点后，childNodes的新增节点、父节点及以前的最后一个子节点的关系指针都会相应地得到更新。更新完成后，appendChild()返回新增的节点。
- 如果需要把节点放在childNodes列表中某个特定的位置上，而不是放在末尾，那么可以使用insertBefore()方法。这个方法接受两个参数：要插入的节点和作为参照物的节点。插入节点后，被插入的节点会变成参照节点的前一个同胞节点(previousSibling)，同时被方法返回。如果参照节点是null，则insertBefore()与appendChild()执行相同的操作。
- replaceChild()方法可以替换节点。该方法接受两个参数：要插入的节点和要替换的节点。要替换的节点将由这个方法返回并从文档树中被移除，同时要插入的节点占据其位置。
- removeChild()方法可以移除节点，该方法接受一个参数，即要移除的节点。被移除的节点将成为方法的返回值。

###### 4. 其他方法
- cloneNode()方法用于创建调用这个方法的节点的一个完全相同的副本。cloneNode()方法接受一个布尔值参数，表示是否执行深复制。在参数为true的情况下，执行深复制，也就是赋值节点机器整个字节点树；在参数为false的情况下，执行浅复制，即只赋值节点本身。复制后返回的节点副本输入文档所有，但并没有为它指定父节点。因此这个节点副本就成为了一个“孤儿”，除非通过appendChild()、insertBefore()或replaceChild()将它添加到文档中。
- cloneNode()方法不会复制添加到DOM节点中的JavaScript属性，例如事件处理程序等。这个方法只复制特性、（在明确指定的情况下也复制）子节点，其他一切都不会复制。IE在此存在一个bug，即他会复制事件处理程序，所以我们建议在复制之前最好先移除事件处理程序。
- normalize()这个方法唯一的作用就是处理文档树中的文本节点。由于解析器的实现或DOM操作等原因，可能会出现文本节点不包含文本，或者接连出现两个文本节点的情况。当在某个节点上调用这个方法时。就会在该节点的后台节点中查找上述两种情况。如果找到了孔文本节点，则删除它；如果找到了相邻的文本节点，则将它们合并为一个文本节点。

##### 10.1.2 Document类型
- JavaScript通过Document类型表示文档。在浏览器中，document对象时HTMLDocument（继承自Document类型）的一个实例，表示整个HTML页面。而且，document对象时window对象的一个属性，因此可以将其作为全局对象来访问。Document节点具有下列特征：
  - nodeType的值为9；
  - nodeName的值为"#document"；
  - nodeValue的值为null；
  - parentNode的值为null；
  - ownerDocument的值为null；
  - 其子节点可能是一个DocumentType（最多一个）、Element（最多一个）、ProcessingInstruction或Comment。
- Document类型可以表示HTML页面或者其他基于XML的文档。不过，最常见的应用还是作为HTMLDocument实例的document对象。通过这个文档对象，不仅可以取得与页面有关的信息，而且还能操作页面的外观及其底层结构。

###### 1. 文档的子节点
- 虽然DOM标准规定Document节点的子节点可以是DocumentType、Element、ProcessingInstruction或Comment，但还有两个内置的访问其子节点的快捷方式。第一个就是documentElement属性，该属性始终指向HTML页面中的\<html\>元素。另一个就是通过childNodes列表访问文档元素，但通过documentElement属性则能更快捷、更直接地访问该元素。
- 作为HEMLDocument的实例，document对象还有一个body属性，直接指向\<body\>元素。
- 所有浏览器都支持document.documentElement和document.body属性。
- Document另一个可能的子节点是DocumentType。通常将\<!DOCTYPE\>标签看成一个与文档其他部分不同的实体，可以通过doctype属性（在浏览器中使document.doctype）来访问它的信息。

###### 2. 文档信息
- 作为HTMLDocument的一个实例，document对象还有一些标准的Document对象所没有的属性。这些属性提供了document对象所表现的网页的一些信息。
- titile属性，包含着\<title\>元素中的文本——显示在浏览器窗口的标题栏或标签页上。通过这个属性可以取得当前页面的标题，也可以修改当前页面的标题并反映在浏览器的标题中。修改title属性的值不会改变\<title\>元素。
- URL属性中包含页面完整的URL（即地址栏中显示的URL）。
- domain属性中只包含页面的域名。
- referrer属性中保存着连接到当前页面的那个页面的URL。在没有来源页面的情况下，referre属性中可能会包含空字符串。
- 所有这些信息都存在于请的HTTP头部，只不过是通过这些属性让我们能够在JavaScript中访问它们而已。
- 只有domain是可以设置的。但由于安全方面的限制，也并非可以给domain设置任何值。

###### 3. 查找元素
- getElementById()，接收一个参数：要取得的元素的ID。如果找到相应的元素则返回该元素，如果不存在带有相应ID的元素，则返回null。ID必须与页面中元素的id特性（attribute）严格匹配，包括大小写。
- getElementsByTagName()，接收一个参数，即要取得元素的标签名，而返回的是包含零或多个元素的NodeList。在HTML文档中，这个方法会返回一个HTMLCollection对象，作为一个“动态”集合。
  - HTMLCollection对象与NodeList非常类似。可以使用方括号语法或item()来访问对象中的项。对象中元素的数量也可以使用length属性取得。
  - HTMLCollection对象还有一个方法叫做namedItem()，使用这个方法可以通过元素的name特性取得集合中的项。
  - 要取得文档中所有元素，可以向getElementsByTagName()中传入"\*"。在JavaScript及CSS中，星号(\*)通常表示“全部”。
- getElementsByName()是只有HTMLDocument类型才有的方法，这个方法会返回带有给定name特性的所有元素。最常使用getElementsByName()方法的情况是取得单选按钮。该方法也会返回一个HTMLCollection。

###### 4. 特殊集合
- 除了属性和方法，document对象还有一些特殊的集合。这些集合都是HTMLCollection对象，为访问文档常用的部分提供了快捷方式，包括：
  - document.anchors，包含文档中所有带name特性的\<a\>元素；
  - document.applets，包含文档中所有的\<applet\>元素，因为不再推荐使用\<applet\>元素，所以这个集合已经不建议使用了；
  - document.forms，包含文档中所有的\<form\>元素，与document.getElementsByTagName("form")得到的结果相同；
  - document.images，包含文档中所有的\<img\>元素，与document.getElementsByTagName("img")得到的结果相同；
  - document.links，包含文档中所有带href特性的\<a\>元素。
- 这个特殊集合始终都可以通过HTMLDocument对象访问到，而且，与HEMLCollection对象类似，集合中的项也会随着当前文档内容的个更新而更新。

###### 5. DOM一致性检测
- 由于DOM分为多个级别，也包含多个部分，因此检测浏览器实现了DOM的哪些部分就十分必要了。document.implementation属性就是为此提供相应信息和功能的对象，与浏览器对DOM的实现直接对应。DOM1级只为document.implementation规定了一个方法，即hasFeature()。这个方法接收两个参数：要检测的DOM功能的名称及版本号。如果浏览器支持给定名称和版本的功能，则该方法返回true。

###### 6. 文档写入
- document对象还有将输出流写入到网页中的能力。这个能力体现在下列4个方法中：write()、writeln()、open()和close()。
- 其中write()和writeln()方法都接收一个字符串参数，即要写入到输出流中的文本。write()会原样写入，而writeln()则会在字符串的末尾添加一个换行符(\\n)。在页面被加载的过程中，可以使用这两个方法向页面中动态地加入内容。还可以使用这两个方法动态的包含外部资源，例如JavaScript文件等。在包含JavaScript文件时不能直接包含字符串"\</script\>"，要把这个字符串分开写。
- 方法open()和close()分别用于打开和关闭网页的输出流。如果是在页面加载期间使用write()或writeln()方法，则不需要用到这两个方法。

##### 10.1.3 Element类型
- Element类型用于表现XML或HTML元素，提供了对元素标签名、子节点及特性的访问。Element节点具有以下特征：
  - nodeType的值为1；
  - nodeName的值为元素的标签名；
  - nodeValue的值为null；
  - parentNode可能是Document或Element；
  - 其子节点可能是Element、Text、Comment、ProcessingInstruction、CDATASection或EntityReference。
- 要访问元素的标签名，可以使用nodeName属性，也可以使用tagName属性；这两个属性会返回相同的值（使用后者主要是为了清晰起见）。
- 在HTML中，标签名始终都以全部大写表示；而在XML（有时候也包括XHTML）中，标签名则始终会与源码中的保持一致。加入不确定自己的脚本会在HTML还是XML文档中执行，最好在比价之前将标签名转换为相同的大小写形式。

###### 1. HTML元素
- 所有HTML元素都由HTMLElement类型表示，不是直接通过这个类型，也是通过它的子类型来表示。HEMLElement类型直接继承自Element并添加了一些属性。添加的这些属性分别对应每个HTML元素中都存在的下列标准特性。
  - id，元素在文档中的位移标识符。
  - title，有关元素的附加说明信息，一般通过工具提示条显示出来。
  - lang，元素内容的语言代码，很少使用。
  - dir，语言的方向，值为"ltr"(left-to-right，从左至右)或"rtl"(right-to-left，从右至左)，也很少使用。
  - className，与严肃的class特性对应，即为元素指定的CSS类。没有将这个属性命名为class，是因为class是ECMAScript的保留字。
- 并不是对所有属性的修改都会在页面中直观地表现出来。对id或lang的修改对用户而言是透明不可见的（假设没有基于它们的值设置的CSS样式），而对title的修改则只会在鼠标移动到这个元素上才会显示出来。对dir的修改会在属性被重写的那一刻，立即影响页面中的文本左右对其方式。修改className时，如果新类关联了与此前不同的CSS样式，那么就会立即应用新的样式。

###### 2. 取得特性
- 每个元素都有一个或多个特性，这些特性的用途是给出相应元素或其内容的附加信息。操作特性的DOM方法主要有三个，分别是getAttribute()、setAttribute()和removeAttribute()。这三个方法可以针对任何特性使用，包括哪些以HTMLElement类型属性的形式定义的特性。
- 传递给getAttribute()的特性名应与实际的特性名相同。因此要想得到class特性值，应该传入"class"而不是"className"，后者只有在通过对象属性访问特性时才用到。
- 通过getAttribute()还可以取得自定义特性（即标准HTML语言中没有的特性）的值。
- 特性的名称是不区分大小写的，即"ID"和"id"代表的都是同一个特性。
- 有两类特殊的特性，它们虽然有对应的属性名，但属性的值与通过getAttribute()返回的值并不相同。
  - 第一类特性就是style，用于通过CSS为元素指定样式。在通过getAttribute()访问时，返回style特性值中包含的是CSS文本，而通过属性来访问它则会返回一个对象。
  - 第二类是onclick这样的事件处理程序。当在元素上使用时，onclick特性中包含的是JavaScript代码，如果通过getAttribute()访问，则会返回相应代码的字符串。而在访问onclick属性时，则会返回一个JavaScript函数。

###### 3. 设置特性
- setAttribute()方法接收两个参数：要设置的特姓名和值。如果特性已经存在，setAttribute()会以指定的值体院现有的值；如果特性不存在，setAttribute()则创建该属性并设置相应的值。
- 通过setAttribute()方法既可以操作HTML特性也可以操作自定义特性。通过这个方法设置的特姓名会被统一转换为小写形式，即"ID"最终会变成"id"。
- 通过removeAttribute()方法用于彻底删除元素的特性。调用这个方法不仅会清除特性的值，而且也会从元素中完全删除特性。

###### 4. attributes属性
- Element类型是使用attributes属性的唯一一个DOM节点类型。attributes属性中包含一个NamedNodeMap，与NodeList类型，也是一个“动态”的集合。元素的每一个特性都由一个Attr节点表示，每个节点都保存在NamedNodeMap对象中。NemedNodeMap对象拥有下列方法。
  - getNamedItem(name)：返回nodeName属性等于name的节点；
  - removeNamedItem(name)：从列表中移除nodeName属性等于name的节点；
  - setNamedItem(nodes)：向列表中添加节点，以节点的nodeName属性为索引；
  - item(pos)：返回位于数字pos位置处的节点。

##### 5. 创建元素
- 使用document.createElement()方法可以创建新元素。这个方法只接收一个参数，即要创建的元素的标签名。这个标签名在HTML文档中不区分大小写，而在XML（包括XHTML）文档中，则是区分大小写的。例如使用下面的代码可以创建一个\<div\>元素。

```javascript
var div = document.createElement("div");
```
- 在使用createElement()方法创建新元素的同时，也为新元素设置了ownerDocument属性。此时还可以操作元素的特性，为它添加更多子节点，以及执行其他操作。
- 可以使用appendChild()、insertBefore()或replaceChild()方法把元素添加到文档树中，一旦添加到文档树中，浏览器就会立即呈现该元素。

###### 6. 元素的子节点
- 元素可以有任意数目的子节点和后代节点，因为元素可以是其他元素的子节点。元素的childNodes属性中包含了它所有子节点，这些子节点有可能是元素、文本节点、注释或处理指令。

##### 10.1.4 Text类型
- 文本节点由Text类型表示，包含的是可以照字面解释的纯文本内容。纯文本中可以包含转义后的HTML字符，但不能包含HTML代码。Text节点具有以下特征：
  - nodeType的值为3；
  - nodeName的值为"#text"；
  - nodeValue的值为节点所包含的文本；
  - parentNode是一个Element；
  - 不支持（没有）子节点。
- 可以通过nodeValue属性或data属性访问Text节点中包含的文本，这两个属性中包含的值相同。对nodeValue的修改也会通过data反映出来，反之亦然。使用下列方法可以操作节点中的文本。
  - appendData(text)：将text添加到节点的末尾。
  - deleteData(offset, count)：从offset指定的位置开始删除count个字符。
  - insertData(offset, text)：在offset指定的位置插入text。
  - replaceData(offset, count, text)：用text替换从offset指定的位置开始到offset+count位置处的文本。
  - splitText(offset)：从offset指定的位置将当前文本节点分成两个文本节点。
  - substringData(offset, count)：提取从offset指定的位置开始到offset+count为止处的字符串。
- 文本节点还有一个length属性，保存着节点中字符的数目。
- 默认情况下，每个可以包含内容的元素最多只能有一个文本节点，而且必须确实有内容存在。

##### 1. 创建文本节点
- 可以使用document.createTextNode()创建新文本节点，这个方法接收一个参数——要插入节点中的文本。

###### 2. 规范化文本节点
- normalize()方法可以将相邻文本节点合并。如果在一个包含两个或多个文本节点的父元素上调用normalize()方法，则会将所有文本节点合并成一个节点，结果节点的nodeValue等于将合并前每个文本节点的nodeValue值拼接起来的值。

##### 10.1.5 Comment类型
- 注释在DOM中使通过Comment类型来表示的。Comment节点具有下列特征：
  - nodeType的值为8；
  - nodeName的值为"#comment"；
  - nodeValue的值是注释的内容；
  - parentNode可能是Document或Element；
  - 不支持（没有）子节点。
- Comment类型与Text类型继承自相同的基类，因此它拥有除splitText()之外的所有字符串操作方法。与Text类型相似，也可以通过nodeValue或data属性来获取注释的内容。

##### 10.1.6 CDATASection类型
- CDATASection类型只针对基于XML的文档，表示的是CDATA区域。与comment类似，CDATASection类型继承自Text类型，因此拥有splitText()之外所有的字符串操作方法。CDATASection节点具有下列特征：
  - nodeType的值为4；
  - nodeName的值为"#cdata-section"；
  - nodeValue的值是CDATA区域中的内容；
  - parentNode可能是Document或Element；
  - 不支持（没有）子节点。

##### 10.1.7 DocumentType类型
- DocumentType类型在Web浏览器中并不常用，仅有Firefox、Safari、Opera和Chrome4.0支持它。DocumentType包含着与文档的doctype有关的所有信息，它具有下列特征：
  - nodeType的值为10；
  - nodeName的值为doctype的名称；
  - nodeValue的值null；
  - parentNode是Document；
  - 不支持（没有）子节点。

##### 10.1.8 DocumentFragment类型
- 在所有节点类型中，只有DocumentFragment在文档中没有对应的标记。DOM规定文档片段(document fragment)是一种“轻量级”的文档，可以包含和控制节点，但不会像完整的文档那样占用额外的资源。DocumentFragment节点具有下列特征：
  - nodeType的值为11；
  - nodeName的值为"#document-fragment"；
  - nodeValue的值为null；
  - parentNode的值为null；
  - 子节点可以是Element、ProcessingInstruction、Comment、Text、CDATASection或EntityReference。
- 虽然不能把文档碎片直接添加到文档中，但可以将它作为一个“仓库”来使用，即可以在里面保存将来可能会添加到文档中的节点。
- 可以使用document.createDocumentFragment()方法来创建文档片段。


##### 10.1.9 Attr类型
- 元素的特性在DOM中以Attr类型来表示。具有下列特征：
  - nodeType的值为11；
  - nodeName的值是特性的名称；
  - nodeValue的值是特性的值；
  - parentNode是null；
  - 在HTML中不支持（没有）子节点；
  - 在XML中子节点可以是Text或EntityReference。
- 尽管它们也是节点，但特性却不被认为是DOM文档树中的一部分。
- Attr对象有3个属性：name、value和specified。其中，name是特性名称（与nodeName的值相同），value是特性的值（与nodeValue的值相同），而specified是一个布尔值，用于区别特性时在代码中指定的还是默认的。

#### 10.2 DOM操作技术
- 很多时候，DOM操作都比较简明，因此用JavaScript生成那些通常原本是用HTML代码生成的内容并不麻烦。不过，也有一些时候，操作DOM并不像表面上看起来那么简单。由于浏览器中充斥着隐藏的陷阱和不兼容问题，用JavaScript代码处理DOM的某些部分要比处理其他部分更复杂一些。不过，也有一些时候，操作DOM并不像表面上看起来那么简单。

##### 10.2.1 动态脚本
- 创建动态脚本有两种方式：插入外部文件和直接插入JavaScript代码。
- 动态加载的外部JavaScript文件能够立即运行。

```javascript
var script = document.createElement("script");
script.type = "text/javascript";
script.src = "client.js";
document.body.appendChild(script);
// 可以使用函数封装
function loadScript(url) {
    var script = document.createElement("script");
    script.type = "text/javascript";
    script.src = url;
    document.body.appendChild(script);
}
// 调用函数加载外部JavaScript文件
loadScript("client.js");
```

##### 10.2.2 动态样式
- 使用DOM代码可以很容易地动态创建出这个元素：

```javascript
var link = document.createElement("link");
link.rel = "stylesheet";
link.type = "text/css";
link.href = "style.css";
var head = document.getElementsByTagName("head")[0];
head.appendChild(link);
// 可以封装成函数
function loadStyles(url) {
    var link = document.createElement("link");
    link.rel = "stylesheet";
    link.type = "text/css";
    link.href = url;
    var head = document.getElementsByTagName("head")[0];
    head.appendChild(link);
}
// 调用函数
loadStyles("style.css");
```

##### 10.2.3 操作表格
- \<table\>元素是HTML中最复杂的结构之一。要想创建表格，一般都必须涉及表示表格行、单元格、表头等方面的标签。由于设计的标签多，因而使用核心DOM方法创建和修改表格旺旺都免不了要编写大量的代码。为了方便构建表格，HTML DOM还为\<table\>、\<tbody\>和\<tr\>元素添加了一些属性和方法：
  - caption：保存着对\<caption\>元素（如果有）的指针。
  - tBodies：是一个\<tbody\>元素的HTMLCollection。
  - tFoot：保存着对\<tfoot\>元素（如果有）的指针。
  - tHead：保存着对\<thead\>元素（如果有〕的指针。
  - rows：是一个表格中所有行的HTMLCollection。
  - createTHead()：创建\<thead\>元素，将其放到表格中，返回引用。
  - createTFoot()：创建\<tfoot\>元素，将其放到表格中，返回引用。
  - createCaption()：创建\<caption\>元素，将其放到表格中，返回引用。
  - deleteTHead()：删除\<thead\>元素。
  - deleteTFoot()：删除\<tfoot\>元素。
  - deleteCaption()：删除\<caption\>元素。
  - deleteRow(pos)：删除指定位置的行
  - insertRow(pos)：向rows集合中的指定位置插入一行。
- 为\<tbody\>元素添加的属性和方法如下。
  - rows：保存着\<tbody\>元素中行的HTMLCollection。
  - deleteRow(pos)：删除指定位置的行。
  - insertRow(pos)：向rows集合中的指定位置插入一行，返回对新插入行的引用。
- 为\<tr元素添加的属性和方法如下。
  - cells：保存着\<tr\>元素中单元格的HTMLCollection。
  - deleteCell(pos)：删除指定位置的单元格。
  - insertCell(pos)：向cells集合中指定位置插入一个单元格，返回对新插入单元格的引用。

##### 10.2.4 使用NodeList
- 理解NodeList及其“近亲”NamedNodeMap和HTMLCollection，是从整体上透彻理解DOM的关键所在。这三个集合都是“动态的”；换句话说每当文档结构发生变化时，它们都会得到更新。因此，它们始终都会保存着最新、最准确的信息。从本质上说，所有NodeList对象都是在访问DOM文档时实时运行的查询。

