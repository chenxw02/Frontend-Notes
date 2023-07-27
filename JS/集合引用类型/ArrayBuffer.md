# `ArrayBuffer`

## 概述

`ArrayBuffer` 是一个二进制数据缓冲区，它与 `DataView` 和 `Typed Array` 一起为 js 提供了处理二进制数据的能力，尤其在与外部设备、网络通信、图像处理和音视频处理等相关的场景中特别有用

## `ArrayBuffer`

* `ArrayBuffer` 是一个通用的、固定长度的二进制数据缓冲区的对象
* `ArrayBuffer` 类似于数组，但不能直接访问或修改其内容
* `ArrayBuffer` 提供了一个用于读取、写入和操作底层数据的接口
* `ArrayBuffer` 在创建时需要指定固定的字节长度，并且不能更改长度。它通常作为二进制数据的容器，比如音频、图像或网络数据等

## `DataView`

* `DataView` 是一个用于读取和写入 `ArrayBuffer` 中的数据的接口，可以通过指定字节偏移量和数据类型的方式来访问 `ArrayBuffer` 中的内容
* `DataView` 允许开发者以不同的数据格式（例如整数、浮点数、字节等）从 `ArrayBuffer` 中读取数据，并将数据写入 `ArrayBuffer`，从而实现对底层二进制数据的灵活操作

## `Typed Array`

* `Typed Array` 是一组特定类型的数组，它们直接关联到 `ArrayBuffer`，并允许直接读取和写入底层二进制数据
* 不同类型的 `Typed Array`（如 `Int8Array`、`Uint8Array`、`Int16Array`、`Float32Array` 等）对应着不同的数据类型和字节长度。这使得开发者可以像操作普通数组一样，对二进制数据进行高效的处理，而无需手动解析字节