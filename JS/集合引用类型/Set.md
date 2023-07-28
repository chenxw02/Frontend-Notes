# `Set`

## 概述

`Set` 是加强版的 `Map`，大多数 API 和行为都是共有的

## 基本 API

* 创建、查询、删除操作与 `Map` 相同
* 添加操作方法名为 `add()` 相同， 行为与 `Map` 中的 `set()` 相同
* 没有 `get()` 方法，只有 `has()` 方法

## 顺序与迭代

使用 `forEach()` 方法可以不使用迭代器进行迭代，接收一个参数，作为回调函数（也可接收第二参数，用于重写回调函数内部 `this` 的值）

```js
const s = new Set(["val1", "val2", "val3"]);

s.forEach((val, dupVal) => alert(`${val} -> ${dupVal}`));
// val1 -> val1
// val2 -> val2
// val3 -> val3
```

无法使用迭代更改 `Set` 中的值

```js
let s = new Set();
s.add("John");
s.add(30);

for (const value of s.values()) {
  if (value === "John") {
   value = "xiaowei"; // TypeError: Assignment to constant variable.
  }
}

console.log(myMap.has("xiaowei")); 
```

如果使用一个 `Object` 作为集合的元素，修改 `Object` 的属性并不会影响其作为集合值得身份（外部的对象和 `Set` 中的对象都是对同一个地址空间的引用，修改外部的对象本身就会反映在 `Set` 内部的对象上）

```js
const obj = { name: "John" };
const s = new Set();
s.add(obj, 30);

obj.name = "xiaowei";

console.log(s.has(obj)); // 输出：true
```

### `values()`

* `Set` 的默认迭代器是 `values()` 方法
* `keys()` 是 `values()` 的别名方法
* `Symbol.iterator` 引用 `values()` 方法

```js
const s = new Set(["val1", "val2", "val3"]);

alert(s.values === s[Symbol.iterator]); // true
alert(s.keys === s[Symbol.iterator]);   // true

for (let value of s.values()) {
  alert(value);
}
// val1
// val2
// val3

for (let value of s[Symbol.iterator]()) {
  alert(value);
}
// val1
// val2
// val3
```

`values()` 是默认迭代器，可以直接对其使用扩展操作，将集合转换为数组

```js
const s = new Set(["val1", "val2", "val3"]);

console.log([...s]); // ["val1", "val2", "val3"]
```

### `keys()`

`keys()` 是 `values()` 的同名方法

### `entries()`

返回类似 `Map` 键值对的形式

```js
const s = new Set(["val1", "val2", "val3"]);

for (let pair of s.entries()) {
  console.log(pair);
}
// ["val1", "val1"]
// ["val2", "val2"]
// ["val3", "val3"]
```

## 定义正式集合操作

