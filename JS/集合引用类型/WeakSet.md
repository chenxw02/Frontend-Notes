# `WeakSet`

## 概述

`WeakSet` 和 `Set` 的关联与 `WeakMap` 和 `Map` 的关联相似

## 基本 API

* 无 `clear()` 方法
* 其它与 `Set`  相同
* 成员（值）的类型必须是 `Object` 或继承自 `Object` 的类型

## 弱值

`WeakSet` 中的对象是弱引用的，这意味着如果一个对象只被 `WeakSet`所引用，而其他地方没有其他引用指向它，那么该对象就会被垃圾回收器回收

```js
// 创建WeakSet并添加对象
const weakSet = new WeakSet();
const obj1 = { name: "John" };
const obj2 = { name: "Alice" };

weakSet.add(obj1);
weakSet.add(obj2);

// weakSet中的对象是弱引用的，如果没有其他引用指向它们，将会被垃圾回收
obj1 = null; // obj1不再被强引用，将会在适当时机被垃圾回收
console.log(weakSet.has(obj1)); // 输出: false
console.log(weakSet.has(obj2)); // 输出: true，obj2仍在WeakSet中
```

## 不可迭代值

`weakMap` 中的键值对在任何时候都可能被销毁，所以它不提供迭代器（也不提供 `clear()` 方法），且迭代方法可能阻碍垃圾回收

## 使用

`WeakSet` 实例的用处没有那么大

### 给对象打标签

```js
const disabledElements = new Set();

const loginButton = document.querySelector('#login');

// 通过加入对应集合，给这个节点打上“禁用”标签
disabledElements.add(loginButton);
```

在这个节点被 DOM 树中被删除后，`Set` 中的引用仍然存在，只有将其显式地删除或 `Set` 本身被销毁时，`Set` 中的引用才会被回收

```js
const disabledElements = new WeakSet();

const loginButton = document.querySelector('#login');

// 通过加入对应集合，给这个节点打上“禁用”标签
disabledElements.add(loginButton);
```

使用 `weakSet`，这个节点在 DOM 树中被删除后，由于没有其它引用，`weakSet` 中的引用也会被回收