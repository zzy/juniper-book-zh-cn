# Juniper

> [README.md](https://github.com/graphql-rust/juniper/blob/master/docs/book/content/README.md)
> <br />
> commit 9623e4d32694118e68ce8706f29e2cfbc6c5b6dc

Juniper 是 Rust 语言的 [GraphQL] 服务器库，用最少量的样板文件和配置构建类型安全且快速的 API 服务器。

[GraphQL][graphql] 是Facebook开发的一种数据查询语言，旨在为移动和 Web 应用程序前端提供服务。

_Juniper_ 使得以 Rust 语言编写类型安全且速度惊人的 GraphQL 服务器成为可能，我们还尝试尽可能方便地声明和解析 GraphQL 模式。

Juniper 不包含 Web 服务器，仅提供了构建快，使得其与已有服务器的集成简单明了。Juniper 可选地为 [Hyper][hyper]、[Iron][iron]、[Rocket]，以及 [Warp][warp]等框架提供了预构建集成，并嵌入了 [Graphiql][graphiql]，以便于调试。

- [Cargo crate](https://crates.io/crates/juniper)
- [API Reference][docsrs]

## 特点

Juniper 根据 [GraphQL 规范定义][graphql_spec]支持完整的 GraphQL 查询语言，包括：接口、联合、模式内省，以及验证。但是不支持模式语言。

Juniper 作为 Rust 语言的 GraphQL 库，默认构建非空类型。类型为 `Vec<Episode>` 的字段将被转换为 `[Episode!]!`，相应的 Rust 语言类型则为 `Option<Vec<Option<Episode>>>`。

## 集成

### 数据类型

Juniper 与一些较常见的 Rust 库进行了自动集成，使构建模式变得简单，被集成的 Rust 库中的类型将在 GraphQL 模式中自动可用。

- [uuid][uuid]
- [url][url]
- [chrono][chrono]

### Web 框架

- [hyper][hyper]
- [rocket][rocket]
- [iron][iron]
- [warp][warp]

## API 稳定性

Juniper 还未发布 1.0 版本，部分 API 稳定性可能不够成熟。

## 译者注

[Juniper 中文手册（https://books.budshome.com/juniper）](https://books.budshome.com/juniper)包含 `Juniper` 中文文档和代码示例，源码放在 [zzy/juniper-book-zh](https://github.com/zzy/juniper-book-zh)，内容译自[官方文档](https://github.com/graphql-rust/juniper/tree/master/docs/book)。

基于 actix-web + juniper + diesel 构建 GraphQL 服务器的模板代码，放置在 github 仓库 [actix-graphql-react](https://github.com/zzy/actix-graphql-react)，部署在[演示站点](https://cms.budshome.com/graphql)。所用技术包括：

- [Rust](https://www.rust-lang.org/zh-CN)
- [actix-web](https://crates.io/crates/actix-web) - Web server
- [juniper](https://crates.io/crates/juniper) - GraphQL server
- [diesel](https://crates.io/crates/diesel) - ORM
- [PostgreSQL](https://postgresql.org) - Database
- [jsonwebtoken](https://crates.io/crates/jsonwebtoken) - JSON Web Token
- [GraphQL Playground](https://github.com/prisma-labs/graphql-playground) - GraphQL UI

## 状态

- 对照源码位置：[https://github.com/graphql-rust/juniper/tree/master/docs/book/content](https://github.com/graphql-rust/juniper/tree/master/docs/book/content)
- 每章翻译开头都带有官方链接和 commit hash，若发现与官方不一致，欢迎 Issue 或 PR :bug:

## 做贡献

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

### 测试配置

手册中的所有 `Rust` 代码示例在 `CI` 上编译，使用了 [skeptic](https://github.com/budziq/rust-skeptic) 库。

## 声明

水平有限，错漏难免，欢迎指教；或请发 [issue到GitHub](https://github.com/zzy/juniper-book-zh)；或者直接联系。

> 电子邮箱：linshi@budshome.com；微信：budshome；QQ：9809920。

感谢`graphql-rust/juniper 团队`的无私奉献。

💥 笔者无意侵犯任何人的权利和利益，故若有不适，请联系我。

------

[graphql]: http://graphql.org
[graphiql]: https://github.com/graphql/graphiql
[iron]: http://ironframework.io
[graphql_spec]: http://facebook.github.io/graphql
[test_schema_rs]: https://github.com/graphql-rust/juniper/blob/master/juniper/src/tests/schema.rs
[tokio]: https://github.com/tokio-rs/tokio
[hyper_examples]: https://github.com/graphql-rust/juniper/tree/master/juniper_hyper/examples
[rocket_examples]: https://github.com/graphql-rust/juniper/tree/master/juniper_rocket/examples
[iron_examples]: https://github.com/graphql-rust/juniper/tree/master/juniper_iron/examples
[hyper]: https://hyper.rs
[rocket]: https://rocket.rs
[book]: https://graphql-rust.github.io
[book_quickstart]: https://graphql-rust.github.io/quickstart.html
[docsrs]: https://docs.rs/juniper
[warp]: https://github.com/seanmonstar/warp
[warp_examples]: https://github.com/graphql-rust/juniper/tree/master/juniper_warp/examples
[actix-web]: https://github.com/actix/actix-web
[uuid]: https://crates.io/crates/uuid
[url]: https://crates.io/crates/url
[chrono]: https://crates.io/crates/chrono
