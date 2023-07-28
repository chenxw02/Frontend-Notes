# Map

## 概述

* 在 ES6 之前，一般使用 `Object` 来存储键值对，即属性作为键，属性的值作为键值
* ES6 新增了 `Map` 类型，用于专门存储键值对
* `Map` 接收任何数据类型作为键（`Object` 只接收数值、字符串或符号作为键）

## 基本 API

### 创建

通过构造函数创建 `Map`

```js
// 空映射
const m1 = new Map();

// 使用嵌套数组初始化
const m2 = new Map([
  ["key1": "val1"],
  ["key2": "val2"],
  ["key3": "val3"],
])

// 使用嵌套数组初始化，但键值对信息为空
// 传入的每一个数组都会被视为一个键值对，其中的第一个元素被视为键
// 这样的映射有一个键 undefined，这个键没有对应的值
const m3 = new Map([[]])

// 自定义迭代器函数
function* studentIterator() {
  yield ["Alice", 85];
  yield ["Bob", 72];
  yield ["Charlie", 90];
}

// 使用自定义迭代器初始化
const m3 = new Map(studentIterator());
```

### 添加

使用 `set()` 添加键值对

```js
const m = new Map();
m.set("Alice", 85);

// 多个 set 可以连用
m.set("Bob", 72).set("Charlie", 90);

// set 可以和构造函数连用
const m1 = new Map().set("Alice", 85);
```

### 删除

* `delete()` 接收一个参数，作为要删除的键值对的键；有这个键值对返回 `true`， 否则返回 `false`
* `clear()` 不接收参数，删除所有键值对

```js
const m = new Map([
  ["Alice", 85],
  ["Bob", 72],
  ["Charlie", 90],
])

const m.delete("Alice");
const m.clear();
```

### 查询

#### `size()`

返回映射中键值对的数量

```js
const m = new Map([
  ["Alice", 85],
  ["Bob", 72],
  ["Charlie", 90],
])

console.log(m.size());  // 3
```

#### `get()`

* 接收一个参数，作为要查询的键值对的键
* 返回查询到的键值对的值，若为查询到则返回 `undefined`

```js
const myMap = new Map();
myMap.set("name", "John");
myMap.set("age", 30);

console.log(myMap.get("name")); // 输出：John
console.log(myMap.get("age"));  // 输出：30
console.log(myMap.get("city")); // 输出：undefined，因为"city"键不存在
```

#### `has()`

* 接收一个参数，作为要查询的键值对的键
* 查询到则返回 `true`，否则返回 `false`

```js
const myMap = new Map();
myMap.set("name", "John");
myMap.set("age", 30);

console.log(myMap.has("name")); // 输出：true，因为"name"键存在
console.log(myMap.has("city")); // 输出：false，因为"city"键不存在
```

#### SameValueZero

在进行查询时，使用 `SameValueZero` 进行键的匹配，规则如下：

* `NaN` 与自身相等
* 其他与全等相同

```js
const myMap = new Map();

const key1 = { name: "John" };
const key2 = { name: "Jane" };
const key3 = NaN;

myMap.set(key1, 42);
myMap.set(key2, "Hello");
myMap.set(key3, "NaN value");

console.log(myMap.get(key1)); // 输出：42，因为 key1 和存储时的 key1 严格相等
console.log(myMap.get({ name: "John" })); // 输出：undefined，因为新对象与存储时的 key1 并非同一个对象
console.log(myMap.get(key2)); // 输出："Hello"，因为 key2 和存储时的 key2 严格相等
console.log(myMap.get(key3)); // 输出："NaN value"，因为 key3 和存储时的 key3 严格相等（对于 NaN 的处理）
```

## 顺序与迭代

`Map` 实例会维护键值对的顺序，可以按照顺序进行迭代操作

* `Map` 提供的迭代器是 `entries()` 
* `Symbol.iterator` 引用 `entries()`

使用 `forEach()` 方法可以不使用迭代器进行迭代，接收一个参数，作为回调函数（也可接收第二参数，用于重写回调函数内部 `this` 的值）

```js
const m = new Map([
  ["key1", "val1"],
  ["key2", "val2"],
  ["key3", "val3"]
]);

m.forEach((val, key) => alert(`${key} -> ${val}`));
// key1 -> val1
// key2 -> val2
// key3 -> val3
```

允许使用 `set()` 更改键值对的值，但不允许直接更改

```js
const myMap = new Map();
myMap.set("name", "John");
myMap.set("age", 30);

for (const [key, value] of myMap.entries()) {
  if (key === "age") {
    // 修改键值对的值
    myMap.set(key, 31);
  }
}

console.log(myMap.get("age")); // 输出：31
```

```js
const myMap = new Map();
myMap.set("name", "John");
myMap.set("age", 30);

for (const [key, value] of myMap.entries()) {
  if (key === "age") {
   	value = 31; // TypeError: Assignment to constant variable.
  }
}
```

无法使用迭代更改 `Map` 中键值对的键

```js
let myMap = new Map();
myMap.set("name", "John");
myMap.set("age", 30);

for (const [key, value] of myMap.entries()) {
  if (key === "age") {
    key = "newAge" // TypeError: Assignment to constant variable.
  }
}
```

如果使用一个 `Object` 作为键值对的键，修改 `Object` 的属性并不会影响映射关系（外部的对象和 `Map` 中的对象都是对同一个地址空间的引用，修改外部的对象本身就会反映在 `Map` 内部的对象上）

```js
const obj = { name: "John" };
const myMap = new Map();
myMap.set(obj, 30);

obj.name = "xiaowei";

console.log(myMap.get(obj)); // 输出：30
```

### `entries()`

`map` 实例提供的迭代器可以通过 `entries()` 或 `[Symbol.iterator]()`取得，迭代器依次迭代每个键值对

```js
const m = new Map([
  ["key1", "val1"],
  ["key2", "val2"],
  ["key3", "val3"]
]);

alert(m.entries === m[Symbol.iterator]); // true

for (let pair of m.entries()) {
  alert(pair);
}
// [key1,val1]
// [key2,val2]
// [key3,val3]

for (let pair of m[Symbol.iterator]()) {
  alert(pair);
}
// [key1,val1]
// [key2,val2]
// [key3,val3]
```

`entries()` 是默认迭代器，可以直接对实例进行扩展，把映射转换为数组

```js
const m = new Map([
  ["key1", "val1"],
  ["key2", "val2"],
  ["key3", "val3"]
]);

console.log([...m]); // [[key1,val1],[key2,val2],[key3,val3]]
```

### `keys()`

迭代键

```js
const m = new Map([
  ["key1", "val1"],
  ["key2", "val2"],
  ["key3", "val3"]
]);

for (let key of m.keys()) {
  alert(key);
}
// key1
// key2
// key3
```

### `values()`

迭代值

```js
const m = new Map([
  ["key1", "val1"],
  ["key2", "val2"],
  ["key3", "val3"]
]);

for (let key of m.values()) {
  alert(key);
}
// value1
// value2
// value3
```

## 比较

### 内存

`Map` 内存性能更优

### 插入性能

`Map` 插入性能更佳

### 查找速度

* `Object` 查找速度更快（大规模时差距较小）

* 在把 `Object` 当成数组使用的情况下（如使用连续整数作为属性），浏览器引起可以进行优化，在内存中使用更高效的布局，`Map` 则不行
* 大量查找操作应使用 `Object`

### 删除性能

* `Map` 的删除性能更佳

* `Object` 删除性能很差，但可以将属性值设置为 `null` 或 `undefined` 进行伪删除
* 大量删除操作应使用 `Map`