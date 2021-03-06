# Modules

## Module 的引用

Filename: src/main.rs
```rust
fn main() {
    // 使用绝对路径引用模块
    // crate 相当于处于根节点的模块
    crate::foo::foo_func();

    // 相对路径引用模块
    self::foo::foo_func();

    // 相对路径并省略 self
    foo::foo_func();


}

mod foo {
    pub fn foo_func() {
        println!("from foo_func");
    }
}
```

## 分解 Module

整个rust项目至少由一个`mod`组成，比如这里的`main`，所以`foo`是`main`的child mod

### 以单个文件作为模块

Filename: src/bar.rs
```rust
// 此时mod的名字就是文件名，不再需要用mod声明了
pub fn bar_func() {
    println!("from bar_func")
}
```

Filename: src/main.rs
```rust
// 告诉编译器加载bar这个mod
// 然后就会在同一目录下，找名字叫`bar`的文件或目录，
mod bar;

fn main() {
    bar::bar_func();
}
```

### 以目录作为模块

目录结构
```
src
|-- main.rs
|-- foo
    |-- mod.rs
    |-- bar.rs
    |-- baz.rs
```

如果模块是一个目录的话，必须定义`mod.rs`这个文件，它是整个模块的入口，起一个索引的作用。如果你了解CommonJS的话，`mod.rs` 就相当于 `index.js`。

Filename: src/foo/mod.rs
```rust
// 将bar作为foo的子模块导出
pub mod bar;

// 将baz下的方法作为foo的方法导出
mod baz;
pub use baz::baz_func;

// 导出foo下的方法
pub fn foo_func() {
    println!("from foo_func");
}
```

Filename: src/foo/bar.rs
```rust
pub fn bar_func() {
    println!("from bar_func");
}
```

Filename: src/foo/baz.rs
```rust
pub fn baz_func() {
    println!("from baz_func");
}
```

Filename: src/main.rs
```rust
mod foo;

fn main() {
    foo::foo_func();
    foo::bar::bar_func();
    foo::baz_func();
}
```
