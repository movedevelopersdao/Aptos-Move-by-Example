# Type Arguments

**Calling Generic Functions**

When calling a generic function, one can specify the type arguments for the function's type parameters in a list enclosed by a pair of angle brackets.

```rust
fun foo() {
    let x = id<bool>(true);
}
```

If you do not specify the type arguments, Move's type inference will supply them for you.

**Using Generic Structs**

Similarly, one can attach a list of type arguments for the struct's type parameters when constructing or destructing values of generic types.

```rust
fun foo() {
    let foo = Foo<bool> { x: true };
    let Foo<bool> { x } = foo;
}
```

If you do not specify the type arguments, Move's type inference will supply them for you.

**Type Argument Mismatch**

If you specify the type arguments and they conflict with the actual values supplied, an error will be given:

```rust
fun foo() {
    let x = id<u64>(true); // error! true is not a u64
}
```

and similarly:

```rust
fun foo() {
    let foo = Foo<bool> { x: 0 }; // error! 0 is not a bool
    let Foo<address> { x } = foo; // error! bool is incompatible with address
}
```

**Unused Type Parameters**

For a struct definition, an unused type parameter is one that does not appear in any field defined in the struct, but is checked statically at compile time. Move allows unused type parameters so the following struct definition is valid:

```rust
struct Foo<T> {
    foo: u64
}
```

This can be convenient when modeling certain concepts. Here is an example:

```rust
module my_addrx::M{
    // Currency Specifiers
    struct Currency1 {}
    struct Currency2 {}

    // A generic coin type that can be instantiated using a currency
    // specifier type.
    //   e.g. Coin<Currency1>, Coin<Currency2> etc.
    struct Coin<Currency> has store {
        value: u64
    }

    // Write code generically about all currencies
    public fun mint_generic<Currency>(value: u64): Coin<Currency> {
        Coin { value }
    }

    // Write code concretely about one currency
    public fun mint_concrete(value: u64): Coin<Currency1> {
        Coin { value }
    }
}

```

In this example, `struct Coin<Currency>` is generic on the `Currency` type parameter, which specifies the currency of the coin and allows code to be written either generically on any currency or concretely on a specific currency. This genericity applies even when the `Currency` type parameter does not appear in any of the fields defined in `Coin`.
