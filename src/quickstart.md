# Quickstart

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

对于更高级的映射，Juniper 提供了多种`宏（macro）`来将 Rust 类型映射到 GraphQL 模式。最重要的宏是 [object][jp_obj_macro] - 过程宏，其用于声明解析器对象，你将使用解析器对象来 `Query` 和 `Mutation` 根元素（roots）。

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
#[graphql(description="A humanoid creature in the Star Wars universe")]
struct Human {
    id: String,
    name: String,
    appears_in: Vec<Episode>,
    home_planet: String,
}

// There is also a custom derive for mapping GraphQL input objects.

#[derive(juniper::GraphQLInputObject)]
#[graphql(description="A humanoid creature in the Star Wars universe")]
struct NewHuman {
    name: String,
    appears_in: Vec<Episode>,
    home_planet: String,
}

// Now, we create our root Query and Mutation types with resolvers by using the
// object macro.
// Objects can have contexts that allow accessing shared state like a database
// pool.

struct Context {
    // Use your real database pool here.
    pool: DatabasePool,
}

// To make our context usable by Juniper, we have to implement a marker trait.
impl juniper::Context for Context {}

struct Query;

#[juniper::object(
    // Here we specify the context type for the object.
    // We need to do this in every type that
    // needs access to the context.
    Context = Context,
)]
impl Query {

    fn apiVersion() -> &str {
        "1.0"
    }

    // Arguments to resolvers can either be simple types or input objects.
    // To gain access to the context, we specify a argument
    // that is a reference to the Context type.
    // Juniper automatically injects the correct context here.
    fn human(context: &Context, id: String) -> FieldResult<Human> {
        // Get a db connection.
        let connection = context.pool.get_connection()?;
        // Execute a db query.
        // Note the use of `?` to propagate errors.
        let human = connection.find_human(&id)?;
        // Return the result.
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
