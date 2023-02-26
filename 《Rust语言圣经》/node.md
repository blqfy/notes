#### 1.3 认识 Cargo
**Rust 语言的包管理工具**
```
$ cargo new world_hello --bin
```
cargo 默认就创建 bin 类型的项目，Rust 项目主要分为两个类型：bin 和 lib，前者是一个可运行的项目，后者是一个依赖库项目。
```
$ cargo run
```
cargo run 执行了编译和运行两个操作，其默认使用 debug 模式进行编译，编译后的文件存储到 ./target/debug 目录，另外有 --release 选项进行编译
```
$ cargo run --release
$ cargo build --release
$ ./target/release/...
```

Cargo.toml 和 Cargo.lock 是 cargo 的核心文件，它的所有活动均基于此二者。

Cargo.toml 是 cargo 特有的项目数据描述文件。它存储了项目的所有元配置信息，如果 Rust 开发者希望 Rust 项目能够按照期望的方式进行构建、测试和运行，那么，必须按照合理的方式构建 Cargo.toml。

Cargo.lock 文件是 cargo 工具根据同一项目的 toml 文件生成的项目依赖详细清单，因此我们一般不用修改它，只需要对着 Cargo.toml 文件撸就行了。

```
use std::ops::{Range, RangeInclusive};
fn main() {
    assert_eq!((1..5), Range{ start: 1, end: 5 });
    assert_eq!((1..=5), RangeInclusive::new(1, 5));
}
```

```
use std::mem::size_of_val;
fn main() {
    let c1 = 'a';
    assert_eq!(size_of_val(&c1),4); 

    let c2 = '中';
    assert_eq!(size_of_val(&c2),4); 

    println!("Success!")
} 
```

```
use std::mem::size_of_val;
fn main() {
    let unit: () = ();
    assert!(size_of_val(&unit) == 0);

    println!("Success!")
}
```

#### 2.3.1 所有权
内存管理三种流派：

* 垃圾回收机制(GC)，在程序运行时不断寻找不再使用的内存，典型代表：Java、Go
* 手动管理内存的分配和释放, 在程序中，通过函数调用的方式来申请和释放内存，典型代表：C++
* 通过所有权来管理内存，编译器在编译时会根据一系列规则进行检查，典型代表：Rust

所有权原则:

* Rust 中每一个值都被一个变量所拥有，该变量被称为值的所有者
* 一个值同时只能被一个变量所拥有，或者说一个值只能拥有一个所有者
* 当所有者(变量)离开作用域范围时，这个值将被丢弃(drop)

:: 是一种调用操作符

 二次释放（double free）（相同）内存会导致内存污染，它可能会导致潜在的安全漏洞。

```
fn main() {
    let mut x = Box::new(5);
    
    let y= &mut (*x);     // 完成该行代码，不要修改其它行！
    
    *y = 4;
    
    assert_eq!(*x, 4);
}
```

```
fn main() {
    let s = give_ownership();
    println!("{}", s);
}

fn give_ownership() -> String {
    let s = String::from("hello, world");
    // convert String to Vec
    // as_bytes() 和 into_bytes() 区别：
    // pub fn as_bytes(&self) -> &[u8] {
    //     &self.vec
    // }
    // pub fn into_bytes(self) -> Vec<u8> {
    //     self.vec
    // }
    let _s = s.as_bytes();
    s
}
```

#### 2.3.2 引用与借用
* 同一作用域，特定数据只能有一个可变引用
* 可变引用与不可变引用不能同时存在
* 编译器优化 NLL
* 引用必须总是有效的（不能悬挂引用）
