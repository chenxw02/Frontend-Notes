# RegExp

## 概述

`RegExp` 类型用于支持正则表达式，`RegExp` 可以直接创建，也可通过构造函数创建

```js
// 匹配第一个"bat"或"cat"，忽略大小写
let pattern1 = /[bc]at/i;

// 跟 pattern1 一样，只不过是用构造函数创建的
let pattern2 = new RegExp("[bc]at", "i");

// 基于已有的表达式，修改标记
let pattern3 = new RegExp(pattern2, "g");
```

## 模式

* `g`：全局模式（默认找到一个匹配就结束）
* `i`：不区分大小写
* `m`：多行模式
* `y`：黏附模式（只查找从 `lastIndex` 开始及之后的字符串）
* `u`：Unicode 匹配
* `s`：dotAll 模式，匹配包括元字符在内的任何字符

进行元字符匹配时，元字符必须进行转义，元字符包括：

`( [ { \ ^ $ | ) ] } ? * + .`

## 属性

### 是否设置了某模式

* `global`
* `ignoreCase`
* `unicode`
* `sticky`
* `multiline`
* `dotAll`

### 其它

* `lastIndex`：下一次搜索的开始位置
* `source`：`pattern` 的字面量字符串，没有开头和结尾的斜杠
* `flags`：标记字符串。

## 方法

### `exec()`

接收：字符串

返回：包含第一个匹配信息的数组或 `null`

```js
const text = '快速的棕色狐狸跳过了懒惰的狗';
const regex = /(\w+)\s(\w+)/;  // 用括号括起来的是两个捕获组
const result = regex.exec(text);
console.log(result);
```

```js
[ '快速 的', '快速', '的', index: 0, input: '快速的棕色狐狸跳过了懒惰的狗', groups: undefined ]

/**
result[0] -> 整个正则表达式匹配到的内容
result[1], result[2] -> 第一个、第二个匹配组匹配到的内容
index -> 匹配到内容在原始字符串中的索引
input -> 原始字符串
groups -> 捕获组信息
*/
```

如果 `pattern` 没有设置全局模式，每次执行都会从 0 开始，返回第一个匹配值；如果设置了全局模式，执行会从上一个匹配值（`lastIndex`）开始，向后搜索下一个匹配值

```JS
let text = "cat, bat, sat, fat";
let pattern = /.at/;

let matches = pattern.exec(text);
console.log(matches.index);      // 0
console.log(matches[0]);         // cat
console.log(pattern.lastIndex);  // 0

matches = pattern.exec(text);
console.log(matches.index);      // 0
console.log(matches[0]);         // cat
console.log(pattern.lastIndex);  // 0
```

```JS
let text = "cat, bat, sat, fat";
let pattern = /.at/g;
let matches = pattern.exec(text);
console.log(matches.index);      // 0
console.log(matches[0]);         // cat
console.log(pattern.lastIndex);  // 3

matches = pattern.exec(text);
console.log(matches.index);      // 5
console.log(matches[0]);         // bat
console.log(pattern.lastIndex);  // 8

matches = pattern.exec(text);
console.log(matches.index);      // 10
console.log(matches[0]);         // sat
console.log(pattern.lastIndex);  // 13
```

如果设置了黏附模式，除第一次执行外，每次执行都只会在 `lastIndex` 处寻找匹配（而不是往后搜索）

```js
let text = "cat, bat, sat, fat";
let pattern = /.at/y;

let matches = pattern.exec(text);
console.log(matches.index);       // 0
console.log(matches[0]);          // cat
console.log(pattern.lastIndex);   // 3

// 以索引 3 对应的字符开头找不到匹配项，因此 exec()返回 null
// exec()没找到匹配项，于是将 lastIndex 设置为 0
matches = pattern.exec(text);
console.log(matches);             // null
console.log(pattern.lastIndex);   // 0

// 向后设置 lastIndex 可以让粘附的模式通过 exec()找到下一个匹配项：
pattern.lastIndex = 5;
matches = pattern.exec(text);
console.log(matches.index);       // 5
console.log(matches[0]);          // bat
console.log(pattern.lastIndex);   // 8
```

### `test()`

接收一个字符串，测试字符串是否与模式匹配

```js
let text = "000-00-0000";
let pattern = /\d{3}-\d{2}-\d{4}/;

if (pattern.test(text)) {
  console.log("The pattern was matched.");
}
```

### `toString()` 和 `toLocaleString()`

返回字面量（即返回 `String`）

通常使用 `toString()`，其它两种方法基本不用

### `valueOf()`

返回正则表达式本身（即返回 `RegExp`）

## 静态属性

*静态属性是指直接定义在构造函数（类）本身上，而不是定义在构造函数实例化的对象上的属性。静态属性在类级别上存在，而不是在对象级别上存在，因此在所有该类的实例之间共享相同的值*

这些静态属性都没有 Web 标准出处，不要再生产环境中使用

### `input`

即 `$_`，最后搜索的字符串

### `lastMatch`

即 `$&`，最后匹配的文本

### `lastParen`

即 `$+`，最后匹配的捕获组

### `leftContext`

即 $\`, `input`字符串匹配文本左边的文本

### `rightContext`

即 `$'`，`input`字符串匹配文本右边的文本

### `$1 ~ $9`

对应序号捕获组匹配的文本