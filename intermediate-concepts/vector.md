# Vector

`vector<T>` is the only primitive collection type provided by Move. A `vector<T>` is a homogenous collection of `T`'s that can grow or shrink by pushing/popping values off the "end".

A `vector<T>` can be instantiated with any type `T`. For example, `vector<u64>`, `vector<address>`, `vector<0x42::MyModule::MyResource>`, and `vector<vector<u8>>` are all valid vector types.

**General `vector` literals:**

Vectors of any type can be created with `vector` literals.

| Syntax               | Type                                                                                                                                                                                             | Description                                |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------ |
| vector\[]            | <table data-header-hidden><thead><tr><th></th></tr></thead><tbody><tr><td><code>vector[]: vector&#x3C;T></code> where <code>T</code> is any single, non-reference type</td></tr></tbody></table> | An empty vector                            |
| vector\[e1, ..., en] | `vector[e1, ..., en]: vector<T>` where `e_i: T` s.t. `0 < i <= n` and `n > 0`                                                                                                                    | A vector with `n` elements (of length `n`) |



In these cases, the type of the `vector` is inferred, either from the element type or from the vector's usage. If the type cannot be inferred, or simply for added clarity, the type can be specified explicitly:

```rust
vector<T>[]: vector<T>
vector<T>[e1, ..., en]: vector<T>
```

**Example vector literals**

```rust
(vector[]: vector<bool>);
(vector[0u8, 1u8, 2u8]: vector<u8>);
(vector<u128>[]: vector<u128>);
(vector<address>[@0x42, @0x100]: vector<address>);
```

**`Vector<u8>` literal**

`vector<u8>` literals are used to represent _byte_ and _hex strings_ in move.

```rust
let byte_string_example:vector<u8> = b"Hello world"; //Byte strings are quoted string literals prefixed by a b
let hex_string_example:vector<u8> = x"48656c6c6f20776f726c64"; //Hex strings are quoted string literals prefixed by a x
```



{% hint style="info" %}
Up-to-date document on various operations on `vector` can be found [here](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/move-stdlib/doc/vector.md#0x1\_vector).
{% endhint %}

```rust
let list = vector::empty<u64>();
        
vector::push_back(&mut list, 10);
vector::push_back(&mut list, 20);

assert!(*vector::borrow(&list, 0) == 10, 9);
assert!(*vector::borrow(&list, 1) == 20, 9);

assert!(vector::pop_back(&mut list) == 20, 9);
assert!(vector::pop_back(&mut list) == 10, 9);
```

**Destroying and copying `vectors`:**

Some behaviors of `vector<T>` depend on the abilities of the element type, `T`. For example, vectors containing elements that do not have `drop` cannot be implicitly discarded like `v` in the example above--they must be explicitly destroyed with `vector::destroy_empty`.

Note that `vector::destroy_empty` will abort at runtime unless `vec` contains zero elements:

```rust
fun destroy_any_vector<T>(vec: vector<T>) {
    vector::destroy_empty(vec) // deleting this line will cause a compiler error
}
```

But no error would occur for dropping a vector that contains elements with `drop`:

```rust
fun destroy_droppable_vector<T: drop>(vec: vector<T>) {
    // valid!
    // nothing needs to be done explicitly to destroy the vector
}
```

Similarly, vectors cannot be copied unless the element type has `copy`. In other words, a `vector<T>` has `copy` if and only if `T` has `copy`. However, even copyable vectors are never implicitly copied:

```rust
let x = vector::singleton<u64>(10);
let y = copy x; // compiler error without the copy!
```

Copies of large vectors can be expensive, so the compiler requires explicit `copy`'s to make it easier to see where they are happening.
