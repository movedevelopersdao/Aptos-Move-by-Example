# Address

`address` is a built-in type in Move that is used to represent locations (sometimes called accounts) in global storage. An `address` value is a 256-bit (32-byte) identifier. At a given address, two things can be stored: Module and Resource.

Although an `address` is a 256-bit integer under the hood, Move addresses are intentionally opaque---they cannot be created from integers, they do not support arithmetic operations, and they cannot be modified. Even though there might be interesting programs that would use such a feature (e.g., pointer arithmetic in C fills a similar niche), Move does not allow this dynamic behavior because it has been designed from the ground up to support static verification.

You can use runtime address values (values of type `address`) to access resources at that address. You _cannot_ access modules at runtime via address values.

**Addresses and their syntax:**

Addresses come in two flavors, _named_ or _numerical_. The syntax for a named address follows the same rules for any named identifier in Move. The syntax of a numerical address is not restricted to hex-encoded values, and any valid `u256`  numerical value can be used as an address value, e.g., `42`, `0xCAFE`, and `2021` are all valid numerical address literals.

To distinguish when an address is being used in an expression context or not, the syntax when using an address differs depending on the context where it's used:

* When an address is used as an expression the address must be prefixed by the `@` character, i.e., `@<numerical_value>`or `@<named_address_identifier>`.
* Outside of expression contexts, the address may be written without the leading `@` character, i.e., `<numerical_value>` or `<named_address_identifier>`.

In general, you can think of `@` as an operator that takes an address from being a namespace item to being an expression item.

```rust
let addrx1:address = @0x1; //numerical address example
let addrx2:address = @my_addrx;//named address example
```

**Global Storage Operations:**

The primary purpose of `address` values are to interact with the global storage operations.

`address` values are used with the `exists`, `borrow_global`, `borrow_global_mut`, and `move_from` operations.

The only global storage operation that _does not_ use `address` is `move_to`, which uses `signer`.

**Ownership:**

As with the other scalar values built-in to the language, `address` values are implicitly copyable, meaning they can be copied without an explicit instruction such as `copy`.
