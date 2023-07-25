# Date

## 概述

Date 保存 UTC 时间 1970 年 1 月 1 日 0 时至今经过的毫秒数

*UTC是世界协调时，没有本地时区的偏移，它表示的是全球的统一时间*

使用 `new Date()` 可以创建一个日期对象，在不给定参数的情况下，该对象保存当前的日期和时间

## `Date.parse()`

`Date.parse()` 接收一个字符串，尝试将字符串转换为表示该日期的毫秒数，支持的格式如下：

* 5/23/2019（月/日/年）
* May 23, 2019
* Tue May 23 2019 00:00:00 GTM-0700
* 2019-05-23T00:00:00

注意：

* 如果传入的字符串不表示日期，`Date.parse()` 返回 `NaN`
* 如果直接将表示日期的字符串传给构造函数（`Date()`），会在后台调用 `Date.parse()`

## `Date.UTC()`

`Date.UTC` 也将日期转换为毫秒数，但接收的参数与 `Date.parse()` 不同：

* 接收参数：年，以 0 为起点的月数，日，分，秒，毫秒
* 参数中只有年和月是必须的，若未指定，日默认为 1 日，其它参数默认为 0

## 继承的方法

### `toString()` 和 `toLocalString()`

* `toString()` 返回 24 小时制、带周几、带时区的时间
* `toLcalString()` 返回 12 小时制、不带周几、不带时区的时间

```js
toLocaleString() - 2/1/2019 12:00:00 AM

toString() - Thu Feb 1 2019 00:00:00 GMT-0800 (Pacific Standard Time)
```

注意：现代浏览器中这两个方法的输出已经趋于一致；在每个浏览器上这些结果的形式可能是不同的

### `valueOf()`

返回日期的毫秒表示

**可用于快捷的比较日期的先后**

## 格式化

下面的格式化特定于实现或地区

* `toDateString()`
* `toTimeString()`
* `toLocaleDateString()`
* `toLocaleTimeString()`
* `toUTCString()`

## 日期/时间组件方法

日期分为普通日期和 UTC 日期

对于 full-year（4 位年份），month，date（几号），day（周几），hours，minutes，seconds，mililseconds 都有获取和设置的方法，以 full-year为例：

* `getFullYear()` 
* `getUTCFullYear()`
* `setFullYear(year)`
* `setUTCFullYear(year)`

其中 day 无法设置；在设置时，如果给出的值超出范围，则发生进位（如设置 hours 为 25，则 hours 为 1，date 进 1）

除此之外，还有：

* `getTime()` -> 返回毫秒表示
* `setTime(milliseconds)` -> 设置毫秒表示
* `getTimezoneOffset()` -> 本地时区偏移，如东 8 区返回 800

