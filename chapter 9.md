### 第9章 客户端检测
- 检测Web客户端的手段很多，而且各有利弊。单最重要的还是要知道，不到万不得已，就不要使用客户端检测。只要能找到更通用的方法，就应该优先采用更通用的方法。一言以蔽之，先设计最通用的方案，然后在使用特定于浏览器的技术增强该方案。

#### 9.1能力检测
- 最常用也最为人们广泛接受的客户端检测形式是**能力检测**（又称特性检测）。能力检测的目标不是识别特定的浏览器，而是识别浏览器的能力。采用这种方式不必顾及特定的浏览器如何如何，只要确定浏览器支持特定的能力，就可以给出解决方案。能力检测的基本模式如下：

```javascript
if (object.propertyInQuestion) {
    // 使用object.propertyInQuestion
}
```
- 举例来说，IE5.0之前的版本不支持document.getElementsById()这个DOM方法。尽管可以使用非标准的document.all属性实现相同的目的，但IE的早期版本中确实不存在document.getElementsById()。于是，也就有了类似下面的能力检测代码：

```javascript
function getElement(id) {
    if (document.getElementById) {
        return document.getElementById(id);
    } else if(document.all) {
        return document.all(id);
    } else {
        throw new Error("No way to retrieve element!");
    }
}
```
- 要理解能力检测，首先必须理解两个重要的概念。如前所述，第一个概念就时先检测达成目的的最常用的特性。对前面的例子来说，就时要先检测document.getElementById()，后检测document.all。先检测最常用的特性可以保证代码最优化，因为在多数情况下都可以避免测试多个条件。
- 第二个重要的概念就时必须测试实际要用到的特性。一个特性存在，不一定意味着另一个特性也存在。

##### 9.1.1 更可靠的能力检测
- 能力检测对于想知道某个特性是否会按照适当方式行事（而不仅仅是某个特性存在）非常有用。
- 在可能的情况下，要尽量使用typeof进行能力检测。特别是，宿主对象没有义务让typeof返回合理的值。

##### 9.1.2 能力检测，不是浏览器检测
- 检测某个或某几个特性并不能够确定浏览器。如果知道自己的应用程序需要使用某些特定的浏览器特性，那么最好是一次性检测所有相关特性，而不要分别检测。
- 在实际开发中，应该将能力检测作为确定下一步解决方案的依据，而不是用它来判断用户使用的是什么浏览器。

#### 9.2 怪癖检测
- 与能力检测类似，**怪癖检测(quirks detection)** 的目标是识别浏览器的特殊行为。但与能力检测确认浏览器支持什么能力不同，怪癖检测是想要知道浏览器存在什么缺陷（“怪癖”也就是bug）。这通常需要运行一小段代码，以确定某一特性不能正常工作。例如，IE8及更早版本中存在一个bug，即如果某个实例属性与标记为[[DontEnum]]的某个原型属性同名，那么该实例属性将不会出现在fon-in循环当中。
- 一般来说，“怪癖”都是个别浏览器所独有的，而且通常被归为bug。在相关浏览器的新版中，这些问题可能不会被修复。由于检测“怪癖”设计运行代码，因此我们建议仅检测那些对你有直接影响的“怪癖”，而且最好在脚本一开始就执行此类检测，以便今早解决问题。

#### 9.3 用户代理检测
- 争议最大的一种客户端检测技术叫做**用户代理检测**。用户代理检测通过检测用户代理字符串来确定实际使用的浏览器。在每一次HTTP请求过程中，用户代理字符串时作为相应首部发送的，而且该字符串可以通过JavaScript的navigator.userAgent属性访问。在服务器端，通过检测用户代理字符串来确定用户使用的浏览器是一种常用而且广为接受的做法。而在客户端，用户代理检测一般被当做一种万不得已才用的做法，其优先级排在鞥努力检测和（或）怪癖检测之后。

##### 9.3.1 用户代理字符串的历史
- HTTP规范（包括1.0和1.1版）明确规定，浏览器应该发送简短的用户代理字符串，指明浏览器的名称和版本号。RFC2616（即HTTP1.1协议规范）是这样描述用户代理字符串的：
> “产品标识符常用于通信应用程序标识自身，由软件名和版本组成，使用产品标识符的大多数领域也允许列出作为应用程序主要部分的子产品，由空格分隔。按照惯例，产品要按照相应的重要程度依次列出，以便标识应用程序。”
- 上述规范进一步规定，用户代理字符串应该以一组产品的形式给出，字符串格式为：标识符/产品版本号。但是，现实中的用户代理字符串则绝没有如此简单。

##### 9.3.2 用户代理字符串检测技术
- 考虑到历史原因以及现代浏览器中用户代理字符串的使用方式，通过用户代理字符串来检测特定的浏览器并不是一件轻松的事。因此，首先要确定的往往是你需要多么具体的浏览器信息。一般情况下，知道呈现引擎和最低限度的版本就足以决定正确的操作方法了。

##### 9.3.3 完整的代码
- 以下是完整的用户代理字符串检测脚本，包括检测呈现引擎、平台、Window操作系统、移动设备和游戏系统。

```javascript
var client = function() {
    // 呈现引擎
    var engine = {
        ie : 0,
        gecko : 0,
        webkit : 0,
        khtml : 0,
        opera : 0,
        // 完整的版本号
        ver : null
    };
    // 浏览器
    var browser = {
        // 主要浏览器
        ie : 0,
        firefox : 0,
        safari : 0,
        konq : 0,
        opera : 0,
        chrome : 0,
        // 具体的版本号
        ver : null
    };
    // 平台、设备和操作系统
    var system = {
        win : false,
        mac : false,
        x11 : false,
        // 移动设备
        iphone : false,
        ipod : false,
        ipad : false,
        ios : false,
        android : false,
        nokiaN : false,
        winMobile : false,
        // 游戏系统
        wii : false,
        ps : false
    };
    // 检测呈现引擎和浏览器
	var ua = navigator.userAgent;
	if (window.opera) {
		engine.ver = browser.ver = window.opera.version();
		engine.opera = browser.opera = parseFloat(engine.ver);
	} else if (/AppleWebKit\/(\S+)/.test(ua)) {
		engine.ver = RegExp["$1"];
		engine.webkit = parseFloat(engine.ver);

		// 确定是Chrome还是Safari
		if (/Chrome\/(\S+)/.test(ua)) {
			browser.ver = RegExp["$1"];
			browser.chrome = parseFloat(browser.ver);
		} else if (/Version\/(\S+)/.test(ua)) {
			browser.ver = RegExp["$1"];
			browser.safari = parseFloat(browser.ver);
		} else {
			// 近似地确定版本号
			var safariVersion = 1;
			if (engine.webkit < 100) {
				safariVersion = 1;
			} else if (engine.webkit < 312) {
				safariVersion = 1.2;
			} else if (engine.webkit < 412) {
				safariVersion = 1.3;
			} else {
				safariVersion = 2;
			}

			browser.safari = browser.ver = safariVersion;
		}
	} else if (/KHTML\/(\S+)/.test(ua) || /Konqueror\/([^;]+)/.test(ua)) {
		engine.ver = browser.ver = RegExp["$1"];
		engine.khtml = browser.konq = parseFloat(engine.ver);
	} else if (/rv:([^\)]+)\) Gecko\/\d{8}/.test(ua)) {
		engine.ver = RegExp["$1"];
		engine.gecko = parseFloat(engine.ver);

		// 确定是不是Firefox
		if (/Firefox\/(\S+)/.test(ua)) {
			browser.ver = RegExp["$1"];
			browser.firefox = parseFloat(browser.ver);
		}
	} else if (/MSIE ([^;]+)/.test(ua)) {
		engine.ver = browser.ver = RegExp["$1"];
		engine.ie = browser.ie = parseFloat(engine.ver);
	}

	// 检测浏览器
	browser.ie = engine.ie;
	browser.opera = engine.opera;

	// 检测平台
	var p = navigator.platform;
	system.win = p.indexOf("Win") == 0;
	system.mac = p.indexOf("Mac") == 0;
	system.x11 = (p == "X11") || (p.indexOf("Linux") == 0);
	// 检测Windows操作系统
	if (system.win) {
		if (/Win(?:dows )?([^do]{2})\s?(\d+\.\d+)?/.test(ua)) {
			if (RegExp["$1"] == "NT") {
				switch (RegExp["$2"]) {
				case "5.0":
					system.win = "2000";
					break;
				case "5.1":
					system.win = "XP";
					break;
				case "6.0":
					system.win = "Vista";
					break;
				case "6.1":
					system.win = "7";
					break;
				default:
					system.win = "NT";
					break;
				}
			} else if (RegExp["$1"] == "9x") {
				system.win = "ME";
			} else {
				system.win = RegExp["$1"];
			}
		}
	}

	// 移动设备
	system.iphone = ua.indexOf("iPhone") > -1;
	system.ipod = ua.indexOf("iPod") > -1;
	system.ipad = ua.indexOf("iPad") > -1;
	system.nokiaN = ua.indexOf("NokiaN") > -1;

	// windows mobile
	if (system.win == "CE") {
		system.winMobile = system.win;
	} else if (system.win == "Ph") {
		if (/Windows Phone OS (\d+.\d+)/.test(ua)) {
			;
			system.win = "Phone";
			system.winMobile = parseFloat(RegExp["$1"]);
		}
	}
	// 检测iOS版本
	if (system.mac && ua.indexOf("Mobile") > -1) {
		if (/CPU (?:iPhone )?OS (\d+_\d+)/.test(ua)) {
			system.ios = parseFloat(RegExp.$1.replace("_", "."));
		} else {
			system.ios = 2; // can’t really detect - so guess
		}
	}

	// 检测Android版本
	if (/Android (\d+\.\d+)/.test(ua)) {
		system.android = parseFloat(RegExp.$1);
	}

	// 游戏系统
	system.wii = ua.indexOf("Wii") > -1;
	system.ps = /playstation/i.test(ua);

	// 返回这些对象
	return {
		engine : engine,
		browser : browser,
		system : system
	};
}();
```

##### 9.3.4 使用方法
- 我们在前面已经强调过了，用户代理检测是客户端检测的最后一个选择。只要可能，都应该优先采用能力检测和怪癖检测用户代理检测一般适用于下列情形。
  - 不能直接准确地使用能力检测或怪癖检测。例如，某些浏览器实现了为将来功能预留的存根（suab）函数。在这种情况下，仅测试相应的数是否存在还得不到足够的信息。
  - 同一款浏览器在不同平台下具备不同的能力。这时候，可能就有必要确定浏览器位于哪个平台下。
  - 为了跟踪分析等目的需要知道确切的浏览器。
