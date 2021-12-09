# chrome工具



# 入口点

## DOM

DOM是html中的文档对象模型，当网页被加载时，浏览器会创建页面的文档对象模型（Document Object Model）。HTML DOM是HTML的标准对象和编程接口，它定义了：

- 作为对象的HTML元素
- 所有HTML元素的属性
- 访问所有HTML元素的方法
- 所有HTML元素的事件

在JS中为了等待DOM事件，需要在文档对象中添加一个事件监听器——DOMContent-Loaded。

```javascript
document.addEventListener("DOMContentLoaded", load);
```

在绑定DOMContentLoaded事件之前，为了安全，还需要检查readyState属性的值：

```javascript
if (document.readyState == "loading") {
    document.addEventListener("DOMContentLoaded", load);
}
```

### 导入外部脚本

在html中导入脚本

```html
<script src = "my-script.js"></script>
```

```html
<script type = "text/javascript">
	let result = 1;
    console.log(result);
</script>
```

在js文件中导入脚本

```javascript
import {myFunction} from "./myJavascript.js"
```

导出(相当于Java中的public，export后可以在其他js中import该对象或方法)

```javascript
export function myFunction() {this.x = 0; this.y = 0;}
```

```javascript
function add(a, b) {return a + b;}
function subtract(a, b) {return a - b;}
function divide(a, b) {return a / b;}
function multiply(a, b) {return a * b;}

export {add, subtract, divide, multiply}
```

### 严格模式

严格模式是ES5及以后可以使用的一个特性，在严格模式下，不可以使用未声明的变量，某些非法语句也不允许。

开启严格模式：

```javascript
// 对函数作用域开启严格模式
function myFunction() {
    'use strict';
    return;
}
```

### 字面量

```javascript
1;					//数字字面量
"string";			//字符串字面量
[];					//数组字面量
{};					//对象字面量
true;				//布尔型字面量
function() {};		//函数是一个值
```

`typeof()`函数可以用来确定字面量的类型。

可以使用对应字面量的构造函数声明对象

```javascript
new Number();
new String();
new Array();
new Object();
new Boolean();
new Function();
```

Json在js中算一个Object对象

### 引用传递

js通过引用进行赋值的，实际上并不会生成原始变量的副本，这一点与Java和C中的不一样。

```javascript
let x = {p:1};
let y = x;
x.p = 2;
console.log(y.p);	//2
```

### 基本类型

js中包含7种数据类型

```js
undefined
null
number
bigint
string
symbol
```

### 模板字符串

在字符串中可以嵌入动态的变量值。

```js
let apples = 10;
let s = `There are ${apples} apples in the basket`;
console.log(s);
```



# 闭包