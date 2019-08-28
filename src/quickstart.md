# 快速入门

> [quickstart.md](https://github.com/graphql-rust/juniper/blob/master/docs/book/content/quickstart.md)
> <br />
> commit 29025e6cae4a249fa56017dcf16b95ee4e89363e

简要介绍 Juniper 中的概念。

## 安装

!文件名 Cargo.toml

```toml
[dependencies]
juniper = "^0.13.1"
```

## 模式示例

要将 Rust 语言的 `enums` 和 `structs` 暴露为 GraphQL，仅需向其增加一个自定义`派生属性`。Juniper 支持将 Rust 语言基本类型轻而易举地映射到 GraphQL 特性，诸如：`Option<T>`、`Vec<T>`、`Box<T>`、`String`、`f64` 和 `i32`、`引用`和`切片（slice）`.

对于更高级的映射，Juniper 提供了多种`宏（macro）`来将 Rust 类型映射到 GraphQL 模式。[过程宏对象][jp_obj_macro]是最重要的宏对象之一，其用于声明解析器对象，你将使用解析器对象来 `Query` 和 `Mutation` 根（roots）。

```rust
use juniper::{FieldResult};

# struct DatabasePool;
# impl DatabasePool {
#     fn get_connection(&self) -> FieldResult<DatabasePool> { Ok(DatabasePool) }
#     fn find_human(&self, _id: &str) -> FieldResult<Human> { Err("")? }
#     fn insert_human(&self, _human: &NewHuman) -> FieldResult<Human> { Err("")? }
# }

#[derive(juniper::GraphQLEnum)]
enum Episode {
    NewHope,
    Empire,
    Jedi,
}

#[derive(juniper::GraphQLObject)]
#[graphql(description="星球大战中的类人生物")]
struct Human {
    id: String,
    name: String,
    appears_in: Vec<Episode>,
    home_planet: String,
}

// 另一个用于映射 GraphQL 输入对象的自定义派生。

#[derive(juniper::GraphQLInputObject)]
#[graphql(description="星球大战中的类人生物")]
struct NewHuman {
    name: String,
    appears_in: Vec<Episode>,
    home_planet: String,
}

// 使用宏对象创建带有解析器的根查询和根变更。
// 对象可以拥有类似数据库池一样的允许访问共享状态的上下文。

struct Context {
    // 这里使用真实数据池
    pool: DatabasePool,
}

// 要让 Juniper 使用上下文，必须实现标注特性（trait）
impl juniper::Context for Context {}

struct Query;

#[juniper::object(
    // 指定对象的上下文类型。
    // 需要访问上下文的每种类型都需如此。
    Context = Context,
)]
impl Query {

    fn apiVersion() -> &str {
        "1.0"
    }

    // 解析器的参数可以是简单类型，也可以是输入对象。
    // 为了访问上下文，我们指定了一个引用上下文类型的参数。
    // Juniper 会自动注入正确的上下文。
    fn human(context: &Context, id: String) -> FieldResult<Human> {
        // 获取数据库连接
        let connection = context.pool.get_connection()?;
        // 执行查询
        // Note the use of `?` to propagate errors.
        let human = connection.find_human(&id)?;
        // 返回结果集
        Ok(human)
    }
}

// Now, we do the same for our Mutation type.

struct Mutation;

#[juniper::object(
    Context = Context,
)]
impl Mutation {

    fn createHuman(context: &Context, new_human: NewHuman) -> FieldResult<Human> {
        let db = executor.context().pool.get_connection()?;
        let human: Human = db.insert_human(&new_human)?;
        Ok(human)
    }
}

// A root schema consists of a query and a mutation.
// Request queries can be executed against a RootNode.
type Schema = juniper::RootNode<'static, Query, Mutation>;

# fn main() {
#   let _ = Schema::new(Query, Mutation{});
# }
```

We now have a very simple but functional schema for a GraphQL server!

To actually serve the schema, see the guides for our various [server integrations](./servers/index.md).

You can also invoke the executor directly to get a result for a query:

## Executor

You can invoke `juniper::execute` directly to run a GraphQL query:

```rust
# // Only needed due to 2018 edition because the macro is not accessible.
# #[macro_use] extern crate juniper;
use juniper::{FieldResult, Variables, EmptyMutation};


#[derive(juniper::GraphQLEnum, Clone, Copy)]
enum Episode {
    NewHope,
    Empire,
    Jedi,
}

// Arbitrary context data.
struct Ctx(Episode);

impl juniper::Context for Ctx {}

struct Query;

#[juniper::object(
    Context = Ctx,
)]
impl Query {
    fn favoriteEpisode(context: &Ctx) -> FieldResult<Episode> {
        Ok(context.0)
    }
}


// A root schema consists of a query and a mutation.
// Request queries can be executed against a RootNode.
type Schema = juniper::RootNode<'static, Query, EmptyMutation<Ctx>>;

fn main() {
    // Create a context object.
    let ctx = Ctx(Episode::NewHope);

    // Run the executor.
    let (res, _errors) = juniper::execute(
        "query { favoriteEpisode }",
        None,
        &Schema::new(Query, EmptyMutation::new()),
        &Variables::new(),
        &ctx,
    ).unwrap();

    // Ensure the value matches.
    assert_eq!(
        res,
        graphql_value!({
            "favoriteEpisode": "NEW_HOPE",
        })
    );
}
```

[hyper]: servers/hyper.md
[warp]: servers/warp.md
[rocket]: servers/rocket.md
[iron]: servers/iron.md
[tutorial]: ./tutorial.html
[jp_obj_macro]: https://docs.rs/juniper/latest/juniper/macro.object.html
