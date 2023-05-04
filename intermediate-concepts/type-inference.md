# Type Inference

In most cases, the Move compiler will be able to infer the type arguments so you don't have to write them down explicitly. Here's what the examples above would look like if we omit the type arguments:

```rust
fun foo() {
    let x = id(true);
    //        ^ <bool> is inferred

    let foo = Foo { x: true };
    //           ^ <bool> is inferred

    let Foo { x } = foo;
    //     ^ <bool> is inferred
}
```

Note: when the compiler is unable to infer the types, you'll need annotate them manually. A common scenario is to call a function with type parameters appearing only at return positions.

```rust
module my_addrx::TypeInference{
    use std::vector;

    fun foo() {
        // let v = vector::empty();
        //                    ^ The compiler cannot figure out the element type.

        let v = vector::empty<u64>();
        //                 ^~~~~ Must annotate manually.
    }
}
```

However, the compiler will be able to infer the type if that return value is used later in that function:

```rust
module my_addrx::TypeInference{
    use std::vector;

    fun foo() {
        let v = vector::empty();
        //                 ^ <u64> is inferred
        vector::push_back(&mut v, 100);
    }
}
```
