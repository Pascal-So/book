## Defining and Instantiating Structs

Structs are similar to tuples, which were discussed in Chapter 3. Like tuples,
the pieces of a struct can be different types. Unlike tuples, we name each
piece of data so it’s clear what the values mean. As a result of these names,
structs are more flexible than tuples: we don’t have to rely on the order of
the data to specify or access the values of an instance.

To define a struct, we enter the keyword `struct` and name the entire struct. A
struct’s name should describe the significance of the pieces of data being
grouped together. Then, inside curly braces, we define the names and types of
the pieces of data, which we call *fields*. For example, Listing 5-1 shows a
struct to store information about a user account:

```rust
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}
```

<span class="caption">Listing 5-1: A `User` struct definition</span>

To use a struct after we’ve defined it, we create an *instance* of that struct
by specifying concrete values for each of the fields. We create an instance by
stating the name of the struct, and then add curly braces containing `key:
value` pairs where the keys are the names of the fields and the values are the
data we want to store in those fields. We don’t have to specify the fields in
the same order in which we declared them in the struct. In other words, the
struct definition is like a general template for the type, and instances fill
in that template with particular data to create values of the type. For
example, we can declare a particular user like this:

```rust
# struct User {
#     username: String,
#     email: String,
#     sign_in_count: u64,
#     active: bool,
# }
#
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
```

To get a specific value from a struct, we can use dot notation. If we wanted
just this user’s email address, we can use `user1.email` wherever we want to
use this value. To change a value in a struct, if the instance is mutable, we
can use the dot notation and assign into a particular field, such as
`user1.email = String::from("someone-else@example.com");`

A convenient way to create a new instance from an old instance, using most of
the old instance's values but changing some values, uses `..` and is known as
*struct update syntax* in this context. For example, to create `user2` by
copying the data from the `user1` instance and changing the values of `email`
and username at the same time, we can specify the new values and then use `..`
to use the remaining values from `user1`:

```rust
# struct User {
#     username: String,
#     email: String,
#     sign_in_count: u64,
#     active: bool,
# }
#
# let user1 = User {
#     email: String::from("someone@example.com"),
#     username: String::from("someusername123"),
#     active: true,
#     sign_in_count: 1,
# };
#
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1
};
```

The struct update syntax is a more concise way of setting each field explicitly
from another instance, since creating new instances that are almost like
existing instances is common. That is, this code is equivalent:

```rust
# struct User {
#     username: String,
#     email: String,
#     sign_in_count: u64,
#     active: bool,
# }
#
# let user1 = User {
#     email: String::from("someone@example.com"),
#     username: String::from("someusername123"),
#     active: true,
#     sign_in_count: 1,
# };
#
let user2 = User {
    email: String::from(""),
    username: String::from("anotherusername567"),
    active: user1.active,
    sign_in_count: user1.sign_in_count,
};
```

We can also define structs that look similar to tuples, called *tuple structs*,
that have the added meaning the struct name provides, but don't have names
associated with their fields, just the types of the fields. The definition of a
tuple struct still starts with the `struct` keyword and the struct name, which
are followed by the types in the tuple. For example, here are definitions and
usages of tuple structs named `Color` and `Point`:

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
```

Note that the `black` and `origin` values are different types, since they're
instances of different tuple structs. Each struct we define is its own type,
even though the fields within the struct have the same types. Otherwise, tuple
struct instances behave like tuples, which we covered in Chapter 3.

We can also define structs that don't have any fields! These are called
*unit-like structs* since they behave similarly to `()`, the unit type.
Unit-like structs can be useful in situations such as when you need to
implement a trait on some type, but you don't have any data that you want to
store in the type itself. We'll be discussing traits in Chapter 10.

> ### Ownership of Struct Data
>
> In the `User` struct definition in Listing 5-1, we used the owned `String`
> type rather than the `&str` string slice type. This is a deliberate choice
> because we want instances of this struct to own all of its data and for that
> data to be valid for as long as the entire struct is valid.
>
> It’s possible for structs to store references to data owned by something else,
> but to do so requires the use of *lifetimes*, a Rust feature that is discussed
> in Chapter 10. Lifetimes ensure that the data referenced by a struct is valid
> for as long as the struct is. Let’s say you try to store a reference in a
> struct without specifying lifetimes, like this:
>
> <span class="filename">Filename: src/main.rs</span>
>
> ```rust,ignore
> struct User {
>     username: &str,
>     email: &str,
>     sign_in_count: u64,
>     active: bool,
> }
>
> fn main() {
>     let user1 = User {
>         email: "someone@example.com",
>         username: "someusername123",
>         active: true,
>         sign_in_count: 1,
>     };
> }
> ```
>
> The compiler will complain that it needs lifetime specifiers:
>
> ```text
> error[E0106]: missing lifetime specifier
>  -->
>   |
> 2 |     username: &str,
>   |               ^ expected lifetime parameter
>
> error[E0106]: missing lifetime specifier
>  -->
>   |
> 3 |     email: &str,
>   |            ^ expected lifetime parameter
> ```
>
> We’ll discuss how to fix these errors so you can store references in structs
> in Chapter 10, but for now, we’ll fix errors like these using owned types like
> `String` instead of references like `&str`.
