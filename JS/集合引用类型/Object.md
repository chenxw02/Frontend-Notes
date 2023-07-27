# `Object`

## 创建

创建一个 `Object`

### 使用构造函数

```js
let person = new Object();
person.name = "xiaowei";
person.age = 22;
```

### 使用字面量

```js
let person = {
    name: "xiaowei",
    age: 22,
};
```

```js
let person = {};
person.name = "xiaowei";
person.age = 22;
```

使用字面量定义对象时，不会实际调用构造函数

**一般使用字面量定义对象**

## 访问

### 使用 `.` 

```js
let name = person.name;
```

### 使用 `[]`

```js
let name = person["name"];  // 必须是属性名的字符串形式
```



## 传递可选参数

```JS
function createUser(options) {
  const defaultOptions = {
    username: 'guest',
    age: 0,
    gender: 'unknown'
  };

  const user = { ...defaultOptions, ...options };
  return user;
}

const user1 = createUser({ username: 'alice', age: 25 });
const user2 = createUser({ username: 'bob', gender: 'male' });
const user3 = createUser({ age: 30 });
```

