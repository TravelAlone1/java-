[TOC]
# ECMAScript

## 语法

### 区分大小写

与Java一样，变量、函数名、运算符以及其他一切东西都是区分大小写的。

比如：变量test与变量TEST是不同的。

### 变量是弱类型的

与Java和C不同，ECMAScript中的变量无特定的类型，定义变量时只用var运算符，可以将它初始化为任意值。

因此，可以随时改变变量所存数据的类型（尽量避免这样做）。

#### 例子

```javascript
var color = "red";
var num = 25;
var visible = true;
```

