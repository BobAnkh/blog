Title: 编程语言Rust介绍与简单入门
Date: 2021-4-6 9:30
Category: Language
Tags: Language, Rust
Slug: blog/rust_intro
Author: BobAnkh
Summary: 编程语言Rust的基本介绍与简单语法入门
Illustration: background.jpg

> 本篇文章主要介绍Rust及其安装和开发环境的配置，加上基本构建过程和简单语法。相对高级的语法诸如所有权、生命周期、Trait、闭包等将留到以后介绍

[TOC]

## 0 概述

**Rust**是一种使每个人都可以构建可靠、高效软件的编程语言。它是一种**系统级编程语言**，注重**高性能**、**可靠性**和**生产力**，支持结构化编程、函数式编程、面向对象编程等多种编程范式。

Rust是**编译型语言**，没有运行时(Runtime)和垃圾回收(Garbage Collector, GC)。Rust使用所有权机制来实现自动内存管理，并以此来保证内存安全。Rust是**开源项目**，目前托管在Github上，Rust官方软件包管理器为**Cargo**。

**高性能**：Rust 速度惊人且内存利用率极高。由于没有运行时和垃圾回收，它能够胜任对性能要求特别高的服务，可以在嵌入式设备上运行，还能轻松和其他语言集成。

**可靠性**：Rust 丰富的类型系统和所有权模型保证了内存安全和线程安全，让您在编译期就能够消除各种各样的错误。

**生产力**：Rust 拥有出色的文档、友好的编译器和清晰的错误提示信息， 还集成了一流的工具——包管理器和构建工具， 智能地自动补全和类型检验的多编辑器支持， 以及自动格式化代码等等。

全世界已有数百家公司在生产环境中使用 Rust，以达到快速、跨平台、低资源占用的目的。很多著名且受欢迎的软件，例如 [Firefox](https://hacks.mozilla.org/2017/08/inside-a-super-fast-css-engine-quantum-css-aka-stylo/)、 [Dropbox](https://blogs.dropbox.com/tech/2016/06/lossless-compression-with-brotli/) 和 [Cloudflare](https://blog.cloudflare.com/cloudflare-workers-as-a-serverless-rust-platform/) 都在使用 Rust。

## 1 特点

### 1.1 零成本抽象

零成本抽象是Rust实现高性能的核心。

### 1.2 所有权模型与内存安全

Rust通过强大的类型系统和所有权模型来保证内存安全与线程安全，在编译时消除很多可能存在的错误。

使用Rust语言编程不需要手动分配(malloc)释放(free)内存和资源。Rust通过所有权系统来判断资源分配和释放的时机，在资源不再使用自动调用析构函数并释放资源。所有权系统是Rust与其他语言最不同的一点，这也是Rust学习曲线较为陡峭的原因之一。

### 1.3 特征(trait)

Rust使用特征(trait)机制来实现抽象和代码复用，而非使用其他编程语言中常见的类(Class)。

特征(trait)的概念接近于其他编程语言的接口概念，trait可以包含方法声明和默认实现，但不能包含成员变量。

### 1.4 基于返回值的错误处理

Rust语言使用返回值来进行错误处理，Rust中可能产生错误或空的函数的返回值一般为`Result<T, E>`或`Option<T>`类型，开发者必须在代码中显式的处理每一种情况，以此保证错误得到处理。

### 1.5 迭代器与闭包

Rust支持多种类型的迭代器、迭代器适配器和消费器。

Rust支持非装箱(Unboxed)闭包。

### 1.6 async/await与Future

Async/await是Rust提供的异步编程方式。

Rust支持异步函数(async function)、异步闭包(async closure)、异步代码块(async block)和在async标记的块中使用.await后置关键词。

Rust只提供了async-await语法和Future特征等必要功能，没有提供默认的事件循环机制/异步运行时(asynchronous run-time)。

### 1.7 Unsafe

强大的类型系统和所有权模型保证了Rust的安全性，但也限制了Rust实现部分功能的能力，比如外部函数接口(FFI)等。对此，Rust提供了unsafe关键词，在unsafe标记的块中，部分操作不会提供安全检查。

### 1.8 宏

Rust支持声明宏、过程宏、编译器插件等机制，显著提升了Rust的表达能力和易用性。

## 2 安装Rust

> 推荐采用[官网](https://www.rust-lang.org/tools/install)上的方式进行安装rustup

**windows**直接下载官网提供的`rustup-init.exe`后运行并根据提示安装。

**Unix**系统可以直接使用`curl`以如下命令安装：`curl https://sh.rustup.rs -sSf | sh`，或在官方[Forge](https://forge.rust-lang.org/infra/other-installation-methods.html)中下载对应的脚本自行安装。

安装完成后，可以运行如下命令进行检测：`rustc -V`。该命令将会输出rustc的版本。同样可以运行如下命令来检测包管理工具Cargo是否正确安装：`cargo -V`。该命令将会输出cargo的版本。

如果仅仅只是想体验这门语言，也可以直接使用官方的在线 [playground](https://play.rust-lang.org/) 进行体验。

## 3 配置Rust开发环境

推荐使用`Visual Studio Code`进行开发，这方面的插件和工具适配的比较好。推荐安装如下插件：

- `bungcip.better-toml` 用来高亮 `Rust` 的 `Toml` 格式配置文件
- `vadimcn.vscode-lldb` 用来调试
- `matklad.rust-analyzer` 或 `rust-lang.rust` 提供语义高亮、代码补全、定义跳转等等一系列功能。两者均非常优秀，选其一使用即可，个人更为推荐使用前者。

## 4 构建Rust的基本流程

### 4.1 创建并初始化新项目

常规而言，推荐采用**Cargo**以命令行方式构建**Rust**项目。

在一个你希望构建Rust项目的路径下，以命令行方式运行如下指令：`cargo new <project>`，此处`<project>`替换为你的项目名称，如：`cargo new hello-world`。

这样会在当前目录下新建一个文件夹名为`hello-world`并完成其内部的一些初始化工作。

同样，也可以自己手动新建一个文件夹，在文件夹内运行`cargo init`命令来完成初始化过程。

初始化过程会构建如下结构：

```console
.
├── Cargo.toml
├── .git
│   └── ...
├── .gitignore
└── src
    └── main.rs
```

`src`文件夹下存放的即是该Rust项目的源码，可以看到此时已经生成了一个`main.rs`文件。此外，Cargo会将该项目置于git管理下，同时会在`.gitignore`中加入忽略`/target`目录（当然如果本项目已经存在`.git`文件夹，即已经被git所管理，Cargo只会修改`.gitignore`文件）。该目录是默认情况下，Rust编译之后产物所在的文件夹。`Cargo.toml`是该项目的配置文件，其内包含的内容如下：

```toml
[package]
name = "hello-world"
version = "0.1.0"
authors = ["author <author@email.com>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]

```

这里记录了项目的名称、版本、作者、使用的 `Rust` 版本和依赖。

`main.rs`中初始化的内容为：

```rust
fn main() {
   println!("Hello, world!");
}
```

这里是一个main函数，是直接调用`main.rs`时的程序入口，`fn`是定义函数的关键字，其内使用了一个宏(Macro)`println!`，用于打印内容并换行。关于宏的内容属于Rust的高级特征，在本文中暂且略去不表。此处是用于输出`"Hello, world!"`到终端。

### 4.2 编译运行项目

我们可以使用`cargo run`命令来编译该文件并运行：

```console
$ cargo run
   Compiling hello-world v0.1.0 (/home/shenyx/hello-world)
    Finished dev [unoptimized + debuginfo] target(s) in 1.04s
     Running `target/debug/hello-world`
Hello, world!
```

也可以使用`cargo build --release`将代码编译为优化后的二进制可执行文件（只编译而并不会运行），可以进入到`target/build/release`目录下（如果不加`--release`参数则也可在`target/build/debug`目录下找到），运行可执行文件：

```console
$ ./hello-world
Hello, world!
```

### 4.3 实用工具

1. **rustfmt**

    在开发过程中，可以使用`rustfmt`根据社区代码风格自动格式化代码。安装`rustfmt`：

    ```shell
    rustup component add rustfmt
    ```

    为了格式化整个Cargo项目：

    ```shell
    cargo fmt
    ```

2. **rustfix**

    在开发过程中，可以使用`rustfix`工具，以命令`cargo fix`使用其来自动采纳编译器警告中给出的修改建议。

3. **clippy**

    `clippy` 工具是一系列 lint 的集合，用于捕捉常见错误和改进 Rust 代码。安装`clippy`：

    ```shell
    rustup component add clippy
    ```

    对于任何Cargo项目可以运行其lint：

    ```shell
    cargo clippy
    ```

> 事实上，cargo还有很多使用，例如`cargo test`可以运行代码内定义的测试等，更多细节建议参考官方指导书 [The Cargo Book](https://doc.rust-lang.org/cargo/)，此处不做过多赘述

## 5. 简单语法介绍

> Rust语言中的注释为使用`//`，后跟注释内容，多行注释需在每行前均加上`//`
>
> Rust中还有文档注释，文档注释使用三斜杠 `///` 而不是两斜杆以支持 Markdown 注解来格式化文本。

### 5.1 定义变量

Rust采用`let name: type = value;`的形式定义变量，例如`let a: i32 = 1;`就定义了一个i32类型（32位有符号整型）的变量a，并赋值为1。

Rust的变量默认是不可变的，如果想要修改变量的值，需要在定义时加上`mut`，即例如：

```rust
let mut b: u32 = 1;
println!("b = {}", b);
b = 3;
println!("b = {}", b);
```

就定义了一个u32类型（32位无符号整型）的变量b，并赋值为1，然后将其修改为3。

常量使用`const`来定义，如`const MAX_POWER: u32 = 10000;`就定义了一个值为10000的u32类型的常量`MAX_POWER`

另外，Rust中，变量名一般采用**snake_case**，常量名一般采用**SCREAMING_SNAKE_CASE**

### 5.2 基本变量类型

最主要的、常用的基本变量类型为：

- 布尔型，bool。

- 字符类型，char。是32bit的，即4个字节，并代表了一个 Unicode 标量值，这意味着它可以比 ASCII 表示更多内容。在 Rust 中，拼音字母（Accented letters），中文、日文、韩文等字符，emoji）以及零长度的空白字符都是有效的 `char` 值。

- 数字，整型包括：

    | 长度    | 有符号  | 无符号  |
    | ------- | ------- | ------- |
    | 8-bit   | `i8`    | `u8`    |
    | 16-bit  | `i16`   | `u16`   |
    | 32-bit  | `i32`   | `u32`   |
    | 64-bit  | `i64`   | `u64`   |
    | 128-bit | `i128`  | `u128`  |
    | arch    | `isize` | `usize` |

    此处有符号表示值可以为负，而无符号则不行。`arch`表示长度随计算机架构而变。

    浮点型包括：

    | 长度   | 浮点数 |
    | ------ | ------ |
    | 32-bit | f32    |
    | 64-bit | f64    |

    Rust不一定要显式指定变量类型，编译器会推测变量类型，如整型默认为i32，浮点型默认为f64等。

    例如定义整型变量a，`let a = 1;`，a就会默认认为是i32类型的；定义浮点型变量b，`let b = 1.1;`，b就会默认认为是f64类型的。

- 数组，[type; size]

    例如`let arr: [u32; 5] = [1, 2, 3, 4, 5];`就定义了一个长度为5，每个元素类型为u32的数组arr，并初始化了值。

    `let arr1 = [3; 5];`也可以以这样的形式定义一个长度为5的数组并令其值均初始化为3。可以使用下标来访问数组元素，如`arr[0]`的值为1。

- 元组，(type1, type2)

    例如`let tup: (i32, f32, u32) = (-2, 1.1, 3);`就定义了一个元组`tup`，可以以`tup.1`这样的形式访问第二个元素1.1。

    可以用模式匹配的方式取出这3个数：`let (x, y, z) = tup;`， x，y，z分别为-2,1.1,3。

### 5.3 函数

> 参数传递等方面的问题涉及到所有权的概念，在这里不做细表

使用`fn`关键字来定义函数，Rust中函数名一般采用**snake_case**。Rust并不关心函数定义在使用它的地方的前面还是后面，只要定义了即可。

定义如下函数`other_func`：

```rust
fn other_func(a: i32, b: u32) -> i32 {
    println!("This is another function!");
    println!("a = {}, b = {}", a, b);
    let result = a + 1;
    result    // 这是Rust推荐写法，较为简洁，也可以写成return result;
}
```

上述函数需要i32类型的参数a和u32类型的参数b，并将a+1的值返回，返回值是i32类型的。使用 `return` 关键字和指定值，可从函数中提前返回；但大部分函数隐式的返回最后的表达式（最后无分号的`result`即是一个表达式）。

可以在`main`函数中如此调用该函数：

```rust
let a: i32 = -1;
let b: u32 = 1;
let result: i32 = other_func(a, b);
println!("result = {}", result);
```

运行这些代码，我们可以看到其输出了`result = 0`在命令行中。

### 5.4 控制流

- if语句

    `if <condition> {<expression>}`。具体例如：

    ```rust
    let x = 1;
    if x == 1 {
        println!("Yes, x = 1!");
    }
    ```

    运行这段代码，可以看到`Yes, x = 1!`被输出在终端上。

- if-else语句

  `if <condition> {<expression>} else {<expression>}`。具体例如：

  ```rust
  let x = 1;
  if x == 2 {
      println!("Yes, x = 2!");
  } else {
      println!("No, x != 2!");
  }
  ```

  运行这段代码，可以看到`No, x != 2!`被输出在终端上。

- if-else if-else语句

    其实就是上面这样结构的嵌套，例如：

    ```rust
    let x = 3;
    if x == 1 {
        println!("Yes, x = 1!")
    } else if x == 2 {
        println!("Yes, x = 2!")
    } else if x == 3 {
        println!("Yes, x = 3!")
    } else {
        println!("No, this is Wrong")
    }
    ```

    运行这段代码，可以看到`Yes, x = 3!`被输出在终端上。

- 在let定义中使用if语句

    ```rust
    let y = 10;
    let condition = true;
    let x = if condition { 
        y + 1 
    } else { 
        6 
    };
    println!("x = {}", x);
    ```

    如上所示，因为要返回值，所以`{}`内的表达式是无分号的（代码块的值是其最后一个表达式的值），但是外部定义的是一个完整的let表达式，所以最后是有分号的。运行这段代码，可以看到`x = 11`被输出在终端上。

- loop循环

    `loop {<expression>}`

    ```rust
    let mut counter: u32 = 0;
    loop {
        println!("Count: {}", counter);
        if counter == 9 {
            println!("Exit Loop!");
            break;
        }
        //counter = counter + 1;
        counter += 1;
    }
    ```

    因为需要计数，所以这里定义了一个可变变量`counter`。在`loop`中，可以使用`break`语句退出循环。运行上述代码，会从`Count: 0`一直打印到`Count: 9`后打印`Exit Loop!`并退出。

- 在let定义中使用loop语句

    ```rust
    let y = loop {
        counter += 1;
        if counter == 20 {
            // return value when break
            break counter * 2;
        }
    };
    println!("y = {}", y);
    ```

    大致同上，只不过这里在break的同时返回了`counter`乘2的值，作为y的值。运行上述代码，可以看到打印出`y = 40`的结果。

- while循环

    `while <condition> {<expression>}`

    ```rust
    let mut i = 0;
    while i != 10 {
        i += 1;
    }
    println!("i = {}", i);
    ```

    此处即在`i`不等于10的时候给`i`不断加1，直到`i`等于10的时候退出while循环。运行上述代码，可以看到打印出`i = 10`的结果

- for循环

    我们可以使用for循环来遍历数组中的元素

    ```rust
    let arr: [u32; 5] = [1, 2, 3, 4, 5];
    for element in arr.iter() {
        println!("element = {}", element);
    }
    for element in &arr {
        println!("element = {}", element);
    }
    ```

    定义了一个数组`arr`并初始化其内元素，主要可以采用上述两种方式顺序取出其内各元素。

## 6 结语

以上就是对于Rust这门编程语言的基本介绍和简单语法入门，更多内容留待后续再言，或可直接阅读官方教程 [The Book](https://doc.rust-lang.org/stable/book/) 来进行更加深入的学习（Rust的官方教程真的写的很好）。
