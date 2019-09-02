# 非结构化对象

> [advanced/non_struct_objects.md](https://github.com/graphql-rust/juniper/blob/master/docs/book/content/advanced/non_struct_objects.md)
> <br />
> commit 29025e6cae4a249fa56017dcf16b95ee4e89363e

Up until now, we've only looked at mapping structs to GraphQL objects. However,
any Rust type can be mapped into a GraphQL object. In this chapter, we'll look
at enums, but traits will work too - they don't _have_ to be mapped into GraphQL
interfaces.

Using `Result`-like enums can be a useful way of reporting e.g. validation
errors from a mutation:

```rust
# #[derive(juniper::GraphQLObject)] struct User { name: String }

#[derive(juniper::GraphQLObject)]
struct ValidationError {
    field: String,
    message: String,
}

# #[allow(dead_code)]
enum SignUpResult {
    Ok(User),
    Error(Vec<ValidationError>),
}

#[juniper::object]
impl SignUpResult {
    fn user(&self) -> Option<&User> {
        match *self {
            SignUpResult::Ok(ref user) => Some(user),
            SignUpResult::Error(_) => None,
        }
    }

    fn error(&self) -> Option<&Vec<ValidationError>> {
        match *self {
            SignUpResult::Ok(_) => None,
            SignUpResult::Error(ref errors) => Some(errors)
        }
    }
}

# fn main() {}
```

Here, we use an enum to decide whether a user's input data was valid or not, and
it could be used as the result of e.g. a sign up mutation.

While this is an example of how you could use something other than a struct to
represent a GraphQL object, it's also an example on how you could implement
error handling for "expected" errors - errors like validation errors. There are
no hard rules on how to represent errors in GraphQL, but there are
[some](https://github.com/facebook/graphql/issues/117#issuecomment-170180628)
[comments](https://github.com/graphql/graphql-js/issues/560#issuecomment-259508214)
from one of the authors of GraphQL on how they intended "hard" field errors to
be used, and how to model expected errors.
