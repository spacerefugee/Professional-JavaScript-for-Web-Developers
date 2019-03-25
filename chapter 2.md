### 第2章 在HTML中使用JavaScript
#### 2.1 <script>元素
- 使用<script>元素向HTML页面插入JavaScript。
- <script>标签6个属性：
1. async：可选属性，立即下载脚本
2. charset：可选属性，表示通过src属性指定的代码的字符集，大多数浏览器会忽略，所以很少人使用
3. defer：可选属性，脚本延迟到文档完全被解析和显示之后再执行
4. language：已废弃，表示代码使用的语言脚本
5. src：可选属性，表示包含要执行代码的外部文件
6. type：可选属性，默认值text/javavscript
- 在使用<script>嵌入JavaScript代码时，不要在代码中的任何地方出现"</script>"字符串。
- 按照惯例，所有<script>元素都应该放在页面的<head>中。

#### 2.2 文档模式
- 文档模式有: 混杂模式(quirks mode)，标准模式(standards mode)和准标准模式(almost standards mode)。
- 如果在文档开始处没有声明文档类型，默认开启混杂模式。
- 准标准模式和标准模式非常接近，差异几乎可以忽略不计。

#### 2.3 <noscript>元素
- 包含<noscript>元素中的内容会在下列情况才会显示出来，其他情况下浏览器不会显示<noscript>中的内容：  
浏览器不支持脚本  
浏览器支持脚本，但脚本被禁用 
