# reqwest库学习

- [reqwest库学习](#reqwest库学习)
  - [rust学习资料](#rust学习资料)
  - [rust学习感受](#rust学习感受)
  - [serde坑](#serde坑)


## rust学习资料
[rust圣经](https://course.rs/about-book.html)

[Rust 程序设计语言](https://kaisery.github.io/trpl-zh-cn/title-page.html)

[通过例子学 Rust 中文版](https://rustwiki.org/zh-CN/rust-by-example/index.html)

[Rust语言开源杂志（2021）](https://rustmagazine.github.io/rust_magazine_2021/)

[Rust Magazine 2022 第一季](https://rustmagazine.github.io/rust_magazine_2022/)

## rust学习感受
最近一直再看[rust圣经](https://course.rs/about-book.html), 已经看得受不了了, 想赶紧实操, 我感觉在实操中学习更有感触

## serde坑
[在新版本的serde中使用derive须要开启对应的features](http://www.javashuo.com/article/p-xvkhztqr-vg.html)

```
serde = { version = "1.0", features = ["derive"] }
```


