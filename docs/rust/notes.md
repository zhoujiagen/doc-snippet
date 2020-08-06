# Notes of Rust

## 1 资源

- Jim Blandy, Jason Orendorff. Programming Rust: Fast, Safe Systems Development. O’Reilly Media: 2018.

### [Learn Rust](https://www.rust-lang.org/learn)

- [the Book](https://doc.rust-lang.org/book/): The Rust Programming Language
- [the Rust standard library](https://doc.rust-lang.org/std/index.html): Comprehensive guide to the Rust standard library APIs.
- [The Rust Reference](https://doc.rust-lang.org/reference/index.html): The Reference is not a formal spec, but is more detailed and comprehensive than the book.
- [The Rustonomicon](https://doc.rust-lang.org/nomicon/index.html): The Rustonomicon is your guidebook to the dark arts of unsafe Rust. It’s also sometimes called “the ’nomicon.”
- [The Rust Unstable Book](https://doc.rust-lang.org/nightly/unstable-book/index.html): The Unstable Book has documentation for unstable features that you can only use with nightly Rust.
- [Command Line Book](https://rust-cli.github.io/book/index.html): Learn how to build effective command line applications in Rust.
- [WebAssembly Book](https://rustwasm.github.io/docs/book/): Use Rust to build browser-native libraries through WebAssembly.
- [Embedded Book](https://doc.rust-lang.org/embedded-book): Become proficient with Rust for Microcontrollers and other embedded systems.

## 2 介绍

### Vocabulary

- ownership: 属主关系
- move: 移动
- borrow: 借
- reference: 引用
- crate: 柳条箱
- module: 模块
- struct: 结构
- enum: 枚举
- pattern: 模式
- traits: 热值
- generics: 泛型
- closure: 闭包
- concurrency: 并发
- macros: 宏
- unsafe code: 不安全的代码

## 3 基本类型

- `i8`,`i16`,`i32`,`i64`, `u8`,`u16`,`u32`,`u64`: 有符号与无符号整数
- `isize`, `usize`: 有符号与无符号整数, 机器地址位数
- `f32`, `f64`: 浮点数
- `bool`: 布尔型
- `char`: Unicode字符, 32位
- `(char, u8, i32)`: 元组, 允许混合类型
- `()`: 单位/空元组
- `struct S { x: f32, y: f32 }`: 命名字段的结构
- `struct T(i32, char)`: 类元组的结构
- `struct E`: 类单位的结构, 没有字段
- ```enum Attend { OnTime, Late(u32) }```: 枚举, 代数数据类型
- `Box<Attend>`: 拥有堆中值的指针
- `&i32`, `&mut i32`: 共享与可变引用
- `String`: UTF-8字符串, 动态大小
- `&str`: `str`的引用, 不拥有UTF-8文本的指针
- `[f64; 4]`, `[u8; 256]`: 数组, 固定长度, 元素类型相同
- `Vec<f64>`: 向量, 变长, 元素类型相同
- `&[u8]`, `&mut [u8]`: 切片的引用, 数组或向量中部分的引用, 由指针和长度构成
- `&Any`, `&mut Read`: 特质对象(实现了一组给定方法的任意值的引用)
- ```fn(&str, usize) -> isize```: 函数指针
- ```|a, b| a*a + b*b```: 闭包示例

### 指针类型

#### 引用(References)

- `&T`: 不可变引用, 类似于C中`const T*`
- `&mut T`: 可变引用, 类似于C中`T*`
- 表达式`&x`: 借一个对`x`的引用
- 给定引用`r`, 解引用`*r`表示`r`指向的值
- 引用在离开作用域时, 不自动释放资源
- Rust的引用从不为null
- 可以引用栈或堆中的值

#### 箱(Boxes)

```
let t = (12, "eggs");
let b = Box::new(t);  // 在堆中分配元组
```

- `b`的类型: `Box<i32, &str>`
- 当`b`离开作用域时, 其内存立即释放, 处理已被移动(例如在函数中返回)

#### 原始指针(Raw Pointers)

- `*mut T`
- `*const T`
- 使用原始指针是不安全的
- 只能在`unsafe`块中借引用原始指针

### 数组, 向量, 切片

- `[T; N]`: `N`个类型为`T`的值的数组. 大小为编译时常量.
- `Vec<T>`: `T`的向量. 动态分配, 大小可变. <br>
`Vec<T>`由三个值构成: 指向堆中存放元素的缓冲区的指针, 缓冲区的容量, 缓冲区的当前长度.
- `&[T]`、`&mut [T]`: `T`的共享切片、`T`的可变切片. 数组或向量中部分值的引用. <br>
切片总是按引用传递. <br>
切片的引用是胖指针: 两个字的值, 指向切片首元素的指针和切片中元素的数量.<br>
- 搜索方法时, 自动将数组、向量转换为切片, 以使用切片上定义的方法.

## 4 属主关系(Ownership)

## 5 引用(References)

## 6 表达式

## 7 错误处理

## 8 柳条箱(Crates)和模块(Modules)

## 9 结构

## 10 枚举和模式

## 11 特质(Traits)和泛型(Generics)

## 12 操作符重载

## 13 工具特质

- `Write`: `write_fmt()`
- `FromStr`: `from_str()`


### `Drop`

### `Sized`

### `Clone`

### `Copy`

### `Deref`, `DerefMut`

### `Default`

### `AsRef`, `AsMut`

### `Borrow`, `BorrowMut`

### `From`, `Into`

### `ToOwned`

### `Cow`


## 14 闭包(Closure)

## 15 迭代器(Iterators)

## 16 集合

## 17 字符串和文本

## 18 输入输出

## 19 并发

## 20 宏(Macros)

## 21 不安全的代码(Unsafe Code)
