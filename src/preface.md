# Juniper 中文手册

中文手册包含 Juniper 中文文档和代码示例。

## 欢迎帮忙

### 需求

本手册由 [mdBook](https://github.com/rust-lang-nursery/mdBook) 编译而成。

如果已有 `Rust` 环境，安装 `mdBook` 请执行命令：

```bash
cargo install mdbook
```

### 启动本地测试服务器

启动持续编译手册并自动加载页面的本地测试服务器，执行命令：

```bash
mdbook serve
```

### 生成手册

将手册渲染输出为 `HTML`，执行命令：

```bash
mdbook build
```

输出目录为：`./docs`。

### 测试

测试手册中的所有代码示例，运行命令：

```bash
cd ./tests
cargo test
```

## 测试配置

手册中的所有 `Rust` 代码示例在 `CI` 上编译，使用了  [skeptic](https://github.com/budziq/rust-skeptic) 库。
