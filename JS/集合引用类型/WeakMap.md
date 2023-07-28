# WeakMap

## 概述

* `WeakMap` 是 ES6 新增的集合类型
* `WeakMap` 是 `Map` 的兄弟类型，其 API 也是 `Map` 的子集
* weak 描述的是垃圾回收程序对键的处理方式

## 基本 API

* 无 `clear()` 方法
* 其它与 `Map`  相同
* 键的类型必须是 `Object` 或继承自 `Object` 的类型
* 保证了只能通过原始的键的对象来访问 `weakMap`

```js
// 初始化是全有或全无的操作
// 只要有一个键无效就会抛出错误，导致整个初始化失败
const wm2 = new WeakMap([
  [key1, "val1"],
  ["BADKEY", "val2"],
  [key3, "val3"]
]);
// TypeError: Invalid value used as WeakMap key
typeof wm2;
// ReferenceError: wm2 is not defined

// 原始值可以先包装成对象再用作键
const stringKey = new String("key1");
const wm3 = new WeakMap([
  stringKey, "val1"
]);
alert(wm3.get(stringKey)); // "val1"
```

## 弱键

* `Map`：`Map` 对象中的键是强引用（strong reference）类型，只要 `Map` 对象存在，键所引用的对象就不会被垃圾回收器回收，键值对也不会被回收，即使这些键没有其他的引用存在
* `WeakMap`：`WeakMap` 对象中的键是弱引用（weak reference）类型。这意味着，如果键没有被其他强引用持有，那么它们（键值对）在垃圾回收时会被自动回收，即使它们是 `WeakMap` 对象的键

```js
// 使用 Map 存储对象引用
function exampleWithMap() {
  const obj1 = { name: "John" };
  const obj2 = { name: "Jane" };

  const myMap = new Map();
  myMap.set(obj1, "Value for John");
  myMap.set(obj2, "Value for Jane");

  // 移除 obj1 的引用，但 myMap 仍然存在
  obj1 = null;

  // 由于 Map 存储了 obj1 的引用，obj1 所引用的对象不会被垃圾回收器回收
  // 即使 obj1 不再被使用，对象依然存在于 Map 中，可能会导致内存泄漏
}
```

```js
// 使用 WeakMap 存储对象引用
function exampleWithWeakMap() {
  let obj1 = { name: "John" };
  const obj2 = { name: "Jane" };

  const myWeakMap = new WeakMap();
  myWeakMap.set(obj1, "Value for John");
  myWeakMap.set(obj2, "Value for Jane");

  // 移除 obj1 的引用
  obj1 = null;

  // 在下一次垃圾回收时，由于 obj1 不再被其他强引用持有，obj1 和关联的值会被垃圾回收器回收
  // WeakMap 中的键值对会被及时地释放，避免了内存泄漏问题
}
```

## 不可迭代键

`weakMap` 中的键值对在任何时候都可能被销毁，所以它不提供迭代器（也不提供 `clear()` 方法），且迭代方法可能阻碍垃圾回收

## 使用

### 私有属性

* 公有属性是可以直接访问的属性

* 私有属性是只能由类内部的方法访问，无法直接访问的属性

```js
class Person {
  constructor(name, age) {
    this.name = name; // 公有属性
    this.age = age;   // 公有属性
  }

  sayHello() {
    console.log(`Hello, my name is ${this.name}. I am ${this.age} years old.`);
  }
}

const person1 = new Person("John", 30);
console.log(person1.name); // 访问公有属性
person1.sayHello();        // 调用公有方法
```

使用 `weakSet` 可以真正实现私有属性，即变量不会被外部访问和修改（因为 `weakMap` 只能通过原始对象进行访问）

```js
const privateData = new WeakMap();

class Person {
  constructor(name, age) {
    // 创建一个私有对象，用于存储私有数据
    const privateObj = {};

    // 将私有数据存储在私有对象中
    privateObj.name = name;
    privateObj.age = age;

    // 将私有对象存储在 WeakMap 中，以实现私有变量的效果
    privateData.set(this, privateObj);
  }

  // 公共方法可以访问私有数据
  getName() {
    return privateData.get(this).name;
  }

  getAge() {
    return privateData.get(this).age;
  }
}

const person = new Person("John", 30);

console.log(person.getName()); // 输出："John"
console.log(person.getAge());  // 输出：30
console.log(person.privateObj); // 输出：undefined，私有对象无法直接访问
```

但在这种实现下，虽无法直接在实例上访问私有属性，但拿到 `WeakMap` 后仍可在外部通过访问 `WeakMap` 来访问私有属性

```js
console.log(privateData.get(person)); // 输出：{ name: 'John', age: 30 }
```

使用 `WeakMap` + 闭包则可实现真正的私有属性。在这种情况下 `weakMap` 本身与外部隔离，外部无法拿到，可以防止在外部通过访问 `WeakMap` 来访问私有属性

```js
const Person = (() => {
  const privateData = new WeakMap();

  class Person {
    constructor(name, age) {
      // 创建一个私有对象，用于存储私有数据
      const privateObj = {};

      // 将私有数据存储在私有对象中
      privateObj.name = name;
      privateObj.age = age;

      // 将私有对象存储在 WeakMap 中，以实现真正的私有属性
      privateData.set(this, privateObj);
    }

    // 公共方法可以访问私有数据
    getName() {
      return privateData.get(this).name;
    }

    getAge() {
      return privateData.get(this).age;
    }
  }

  return Person;
})();

const person1 = new Person("John", 30);
console.log(person1.name); // 输出：undefined，私有属性在外部不可访问
console.log(person1.getName()); // 输出："John"
console.log(person1.getAge()); // 输出：30
```

### DOM 节点元数据

 `weakMap` 不会妨碍来垃圾回收，非常适合保存关联元数据

```js
const m = new Map();

const loginButton = document.querySelector('#login');

// 给这个节点关联一些元数据
m.set(loginButton, {disabled: true});
```

在这个节点被 DOM 树中被删除后，`Map` 中的引用仍然存在，只有将其显式地删除或 `Map` 本身被销毁时，`Map` 中的引用才会被回收

```js
const wm = new WeakMap();

const loginButton = document.querySelector('#login');

// 给这个节点关联一些元数据
wm.set(loginButton, {disabled: true});
```

使用 `weakMap`，这个节点在 DOM 树中被删除后，由于没有其它引用，`weakMap` 中的引用也会被回收