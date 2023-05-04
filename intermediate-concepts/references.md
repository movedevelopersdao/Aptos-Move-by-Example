# References

Move provides two types of references:

* immutable `&` - cannot modify underlying values (or any of its fields).
* mutable `&mut` - allow for modifications via a write through that reference.

**Reference Operators:**

Here the notation `e: T` stands for "expression `e` has type `T`".

| Syntax      | Type                                                  | Description                                                    |
| ----------- | ----------------------------------------------------- | -------------------------------------------------------------- |
| `&e`        | `&T` where `e: T` and `T` is a non-reference type     | Create an immutable reference to `e`                           |
| `&mut e`    | `&mut T` where `e: T` and `T` is a non-reference type | Create a mutable reference to `e`.                             |
| `&e.f`      | `&T` where `e.f: T`                                   | Create an immutable reference to field `f` of struct `e`.      |
| `&mut e.f`  | `&mut T` where `e.f: T`                               | Create a mutable reference to field `f` of struct`e`.          |
| `freeze(e)` | `&T` where `e: &mut T`                                | Convert the mutable reference `e` into an immutable reference. |

The `&e.f` and `&mut e.f` operators can be used both to create a new reference into a struct or to extend an existing reference:

```rust
let s = S { f: 10 };
let f_ref1: &u64 = &s.f; // works
let s_ref: &S = &s;
let f_ref2: &u64 = &s_ref.f // also works
```

A reference expression with multiple fields works as long as both structs are in the same module:

```rust
struct A { b: B }
struct B { c : u64 }
fun f(a: &A): &u64 {
  &a.b.c
}
```

Finally, note that references to references are not allowed:

```rust
let x = 7;
let y: &u64 = &x;
let z: &&u64 = &y; // will not compile
```

**Reading and writing through references:**

Both mutable and immutable references can be read to produce a copy of the referenced value.

Only mutable references can be written. A write `*x = v` discards the value previously stored in `x` and updates it with `v`.

| Syntax    | Type                                                                                 | Description                         |
| --------- | ------------------------------------------------------------------------------------ | ----------------------------------- |
| `*e`      | `T` where `e` is `&T` or `&mut T`                                                    | Read the value pointed to by `e`    |
| \*e1 = e2 | <p><code>()</code> where <code>e1: &#x26;mut T</code> and <code>e2: T</code><br></p> | Update the value in `e1` with `e2`. |

**Freeze inference:**

A mutable reference can be used in a context where an immutable reference is expected:

```rust
let x = 7;
let y: &u64 = &mut x;
```

This works because the under the hood, the compiler inserts `freeze` instructions where they are needed.
