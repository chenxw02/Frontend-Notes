# `Array`

## 创建

### 使用构造函数

```js
let arr1 = new Array();
let arr2 = new Array(5);  // 传入数组长度，此时数组为 [, , , , ,]
let arr3 = new Array("yellow", "green", "blue");  // 传入初始值
```

### 数组字面量

```js
let arr1 = [];
let arr2 = [,,,,,];  // 创建 5 个空位
let arr3 = ["yellow", "green", "blue"]
```

**使用字面量创建数组不会调用构造函数**

### `array.from()`

从一个类似数组的对象（如字符串、类数组对象）和可迭代对象（`set`，`Map`，`NodeList`）创建一个数组

* 接收一个参数，为需要创建数组的对象
* 可以接收第二参数，可用于对原始对象的每个元素进行映射转换

```js
const arrayLike = { 0: 'a', 1: 'b', 2: 'c', length: 3 };
const newArray = Array.from(arrayLike);
console.log(newArray); // 输出：['a', 'b', 'c']
```

```js
const str = 'hello';
const asciiArray = Array.from(str, char => char.charCodeAt(0));

console.log(asciiArray); // 输出：[104, 101, 108, 108, 111]
```

### `array.of()`

用一组参数构造数组，解决了数组构造函数的一些歧义问题

```js
// 使用 Array() 构造函数创建数组，传递单个参数时会产生歧义
const numbers1 = Array.of(5);
const numbers2 = Array(5);

console.log(numbers1); // 输出：[5]
console.log(numbers2); // 输出：[ , , , , ] （长度为 5 的数组，但元素都为空）
```

补充：空位

ES6 中空位被解析为 `undefined`，ES6 之前会忽略空位，且不同方法对空位的处理方式不同

最佳实践：不要使用空位，需要使用时用 `undefined` 显式声明

## 索引

js 的数组长度是可变的，当访问超出数组元素的值时，会返回 `undefined`，而不是报错（C 语言则会直接报错），事实上，所有数组中不存在的元素都会返回 `undefined`

```js
let arr = ["yellow", "green", "blue"];

consloe.log(arr[3]);  // undefined
arr[3] = "black";  // 相当于在数组后追加了一个元素
arr[100] = "red";
consloe.log(arr[99]);  // undefined
```

### `length`

`length` 属性是可以修改的，可以通过修改 `length` 的值来添加或删除元素

```js
let arr = ["yellow", "green", "blue"];  // length = 3

arr.length = 2;  // 相当于删除了最后一个元素
arr.length = 4;  // 相当于追加了一个元素，这个元素为 undefined
arr[arr.length] = "black";  // 相当于追加了一个元素，这个元素为 black
```

## 检测

### `instanceOf`

在同一个框架（`iframe`）内可以直接使用。在不同框架之间，由于不同框架的 `Array` 的构造函数可能不同，可能发生意想不到的结果

```js
<!-- A 框架的代码 -->
<script>
  const arr = [1, 2, 3];
  // 将数组传递给 B 框架
  window.parent.frames[1].postMessage(arr, '*');
</script>

<!-- B 框架的代码 -->
<script>
  window.addEventListener('message', (event) => {
    // 使用 instanceof 操作符检查数组类型
    console.log(event.data instanceof Array); // 可能输出 false
  });
</script>
```

### `isArray()`

接收一个变量，判断它是不是数组，不受框架影响

## 迭代器

### `keys()`

数组索引的迭代器

```js
let arr = ["yellow", "green", "blue"];
const arrKeys = Array.from(arr.keys());

console.log(arrKeys);  // [0, 1, 2]
```

### `values()`

数组元素的迭代器

```js
let arr = ["yellow", "green", "blue"];
const arrValues = Array.from(arr.keys());

console.log(arrValues);  // ["yellow", "green", "blue"]
```

### `entries()`

数组索引 + 元素（键值对）的迭代器

```js
let arr = ["yellow", "green", "blue"];
const arrEntries = Array.from(arr.keys());

console.log(arrEntries);  // [[0, "yellow"], [1, "green"], [2, "blue"]]
```

使用解构拆分键值对（仅 ES6）

```js
for (const [idx, element] of arr.entries()){
    console,log(idx);
    console.log(element);
}
```

## 复制和填充

### `copyWith()`

在数组内部进行元素复制和替换（直接修改原始数组，但不会改变数组的长度），接收三个参数：

- `target`：粘贴的目标位置，即将要替换数据的索引位置
- `start`：复制的起始位置，即从该索引开始复制数据
- `end`：复制的结束位置（不包含该索引），即复制到该索引之前的位置

```js
const arr = [1, 2, 3, 4, 5];
arr.copyWithin(0, 3); // 将索引为3及之后的元素[4, 5]复制到索引为0的位置，得到新的数组 [4, 5, 3, 4, 5]
```

### `fill()`

将指定的值填充到数组中的指定区域（直接修改原始数组，但不会改变数组的长度），接收三个参数：

* `value`：要填充到数组中的值
* `start`：表示开始填充的索引，即从该索引开始向后填充数据。如果为负数，则表示从数组末尾开始计算的索引。如果不提供该参数，默认为0，从数组的第一个元素开始填充
* `end`：表示填充的结束索引（不包含该索引），即填充到该索引之前的位置。如果为负数，则表示从数组末尾开始计算的索引。如果不提供该参数，默认为数组的长度，即填充到数组的末尾

```js
const arr = [1, 2, 3, 4, 5];
arr.fill(0, 1, 3); // 将索引为1和2的元素替换为0，得到新的数组 [1, 0, 0, 4, 5]
```

## 转换

当数组中存在 `null` 或 `undefined` 时，该元素会被转换为空字符串表示，而不会转换为对应的字符串形式

### `valueOf()`

返回数组本身

### `toString()`

* 对每个元素调用 `toString()`
* 返回逗号连接的字符串

### `toLocaleString()`

* 对每个元素调用 `toLocaleString()`
* 返回逗号连接的字符串

### `join()`

* 接收一个参数作为分隔符
* 未传入参数或传入 `undefined` 时以逗号为分隔符
* 对每个元素调用 `toString()`
* 返回指定分隔符连接的字符串

## 栈方法

### `push()`

接收任意数量参数，将其追加到数组末尾，并返回数组最新长度

### `pop()`

删除数组的最后一个元素，并返回被删除的元素

## 队列方法

### `push()`

与上文的 `push()` 是同一方法

### `shift()`

删除数组的第一个元素，并返回被删除的元素

### `unshift()`

接收任意数量参数，将其插入到数组开头，并返回数组最新长度

## 排序

### `reverse()`

反向排列，对原始数组进行操作

### `sort()`

* 升序排列，对原始数组进行操作

* 对每个元素调用 `toString()`，将它们都转换为字符串后再进行比较和排序

* 可以接收一个比较函数，这个函数指明了再什么情况下一个元素应该排在前面

比较函数接收两个参数（以 a 和 b 为例），为需要比较的两个值，返回值如下：

* 负数：a 在前面
* 正数：b 在前面
* 0：不做处理

```js
function compare(a, b) {
  if (a < b) {
    return 1;
  } else if (a > b) {
    return -1;
  } else {
    return 0;
  }
}

const numbers = [5, 2, 8, 1, 4];
numbers.sort(compare);
console.log(values);  // 15,10,5,1,0
```

```js
const numbers = [5, 2, 8, 1, 4];

// 使用比较函数进行升序排序
numbers.sort((a, b) => a - b);

console.log(numbers); // 输出：[1, 2, 4, 5, 8]
```

## 操作

### `conact()`

* 接收多个参数，返回一个原始数组追加上传入的元素的新数组
* 接收的每一个参数都视作一个数组元素，但在默认情况下，对象和数组会被打平为一个一个的数组元素进行追加，而非整体作为一个数组元素进行追加

打平数组的行为来自于数组上的 `Symbol.isConcatSpreadable`，重写这个符号能改变 `concat()` 的行为

阻止 `concat()` 打平

```js
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

arr2[Symbol.isConcatSpreadable] = false; // 阻止 arr2 被打平
const result = arr1.concat(arr2);

console.log(result); // 输出：[1, 2, 3, [4, 5, 6]]
```

强制 `concat()` 打平

```js
const arr1 = [1, 2, 3];
const obj = { length: 3, 0: 4, 1: 5, 2: 6 };

obj[Symbol.isConcatSpreadable] = true; // 强制打平 obj
const result = arr1.concat(obj);

console.log(result); // 输出：[1, 2, 3, 4, 5, 6]
```

### `slice()`

* 用于创建一个包含原数组中部分元素的新数组，即对原始数组没有影响
* 接收一个参数，作为返回元素的开始索引
* 可以接受第二个参数，作为返回元素的结束索引；不传入第二参数时，默认索引到数组结束

### `splice()`

* `splice()` 的工作原理为删除后插入（可选），故有多种用途
* `splice()` 是对原始数组进行操作，返回的是被删除的元素（没有被删除的元素则返回空数组）

#### 删除

传入两个参数，第一个参数为开始位置，第二个参数为要删除元素的数量

#### 插入

传入三个参数，第一个参数为开始位置，第二个参数为 0 （其实是要删除元素的个数），第三个参数为要插入的元素；在第三个参数后还可以有更多参数，以一次插入多个元素

#### 替换

传入三个参数，第一个为开始位置，第二个参数为被替换的元素个数，第三个即以后的参数为要替换进去（插入）的元素

## 搜索和位置

### 按严格相等搜索

在比较过程中使用全等

#### `indexOf()`

* 接收一个参数，作为要查找的元素
* 可以接收第二个参数，作为开始搜索的位置
* 从头开始搜索，返回第一匹配项的索引；未找到返回 -1

#### `lastIndexOf()`

* 与 `indexOf()` 相同
* 从尾部开始搜索

#### `includes()`

* ES7 新增
* 从头开始搜索，返回布尔值

### 按断言函数搜索

断言函数决定一个元素是否匹配

#### `find()`

* 接收一个参数（一个回调函数），作为定义查找的条件
* 回调函数被依次传入数组中的每个元素，函数返回 `true`，则找到
* 返回第一个匹配的元素（即找到则立即停止搜索），未找到返回 `undefined`

回调函数可以接收三个参数：

* `element`：当前遍历的数组元素
* `index`：当前遍历的数组元素的索引
* `array`：原数组本身

```js
const arr = [3, 8, 1, 6, 4];

const result = arr.find((element) => element > 5);

console.log(result); // 输出：8
```

#### `findIndex()`

* 与 `find()` 相同
* 返回索引，未找到则返回 -1

## 迭代

* 迭代方法接收一个参数（一个回调函数）
* 迭代方法的返回由不同的迭代方式决定
* 迭代方法不影响原始数组

### `every()`

* 数组每个元素被依次传入函数
* 对每个元素都返回 `true`，方法返回 `true`

```js
const arr = [2, 4, 6, 8, 10];

const allEven = arr.every((element) => {
  return element % 2 === 0;
});

console.log(allEven); // 输出：true，因为所有元素都是偶数
```

### `filter`

* 数组每个元素被依次传入函数
* 函数返回 `true` 的元素被组成数组返沪

```js
const arr = [1, 2, 3, 4, 5];

const evenNumbers = arr.filter((element) => {
  return element % 2 === 0;
});

console.log(evenNumbers); // 输出：[2, 4]
```

### `forEach()`

* 数组每个元素被依次传入函数
* 没有返回

```js
const arr = [1, 2, 3, 4, 5];

arr.forEach((element) => {
  console.log(element);
});
// 输出：
// 1
// 2
// 3
// 4
// 5
```

### `map()`

* 数组每个元素被依次传入函数
* 对每个元素有一个返回值，方法返回这些返回值组成的数组

```js
const arr = [1, 2, 3, 4, 5];

const squaredArr = arr.map((element) => {
  return element * element;
});

console.log(squaredArr); // 输出：[1, 4, 9, 16, 25]
```

### `some()`

* 数组每个元素被依次传入函数
* 其中一个元素返回 `true`，方法返回 `true`

```js
const arr = [1, 2, 3, 4, 5];

const hasEvenNumber = arr.some((element) => {
  return element % 2 === 0;
});

console.log(hasEvenNumber); // 输出：true
```

## 归并

归并方法用于对数组元素进行累积操作，并将最终结果返回

### `reduce()`

* 接收一个参数（一个回调函数），作为归并函数
* 可以接收第二个参数，作为归并的初始值（即 accumulator 的初始值）如果没有传入第二个参数，归并将从数组第二项开始，第一项则作为 accumulator 的初始值
* 从头开始遍历到最后一项

归并函数接收四个参数：

* accumulator：累积值
* currentValue：当前元素
* currentIndex：当前元素索引
* array：数组本身

```js
const arr = [1, 2, 3, 4, 5];

// 使用 reduce() 方法求和
const sum = arr.reduce((accumulator, currentValue) => {
  return accumulator + currentValue;
}, 0);

console.log(sum); // 输出：15，因为 1 + 2 + 3 + 4 + 5 = 15
```

### `reduceRight()`

* 与 `reduce()` 相同
* 从尾部遍历到第一项

```js
// 使用 reduceRight() 方法拼接字符串
const concatenatedStr = arr.reduceRight((accumulator, currentValue) => {
  return accumulator + currentValue.toString();
}, '');

console.log(concatenatedStr); // 输出："54321"，因为 5 + "4" + "3" + "2" + "1" = "54321"
```

