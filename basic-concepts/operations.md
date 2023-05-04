# Operations

**Arthmetic**

Each of these types supports the same set of checked arithmetic operations. For all of these operations, both arguments (the left and right side operands) _must_ be of the same type. If you need to operate over values of different types, you will need to first perform a cast. Similarly, if you expect the result of the operation to be too large for the integer type, perform a cast to a larger size before performing the operation.

All arithmetic operations abort instead of behaving in a way that mathematical integers would not (e.g., overflow, underflow, divide-by-zero).

| Syntax | Operation           | Aborts If                                |
| ------ | ------------------- | ---------------------------------------- |
| `+`    | addition            | Result is too large for the integer type |
| `-`    | subtraction         | Result is less than zero                 |
| `*`    | multiplication      | Result is too large for the integer type |
| `%`    | modular division    | The divisor is `0`                       |
| `/`    | truncating division | The divisor is `0`                       |

```rust
module my_addrx::Operations
{
    use std::debug::print;

    fun arthmetic_operations(a:u64,b:u64)
    {
        let ans=a+b;       print(&ans);
        let ans=a-b;       print(&ans);
        let ans=a*b;       print(&ans); 
        let ans=a/b;       print(&ans);
        let ans=a%b;       print(&ans);

    }

    #[test]
    fun testing()
    {
        arthmetic_operations(10,3);
    }
}
```

**Bitwise**

The integer types support the following bitwise operations that treat each number as a series of individual bits, either 0 or 1, instead of as numerical integer values.

Bitwise operations do not abort.

| Syntax | Operation   | Description                                           |
| ------ | ----------- | ----------------------------------------------------- |
| `&`    | bitwise and | Performs a boolean and for each bit pairwise          |
| `\|`   | bitwise or  | Performs a boolean or for each bit pairwise           |
| `^`    | bitwise xor | Performs a boolean exclusive or for each bit pairwise |

```rust
module my_addrx::Operations
{
    use std::debug::print;

    fun bitwise_operations(a:u64,b:u64)
    {
        let ans=a|b;       print(&ans);
        let ans=a&b;       print(&ans);
        let ans=a^b;       print(&ans);  

    }

    #[test]
    fun testing()
    {
        bitwise_operations(1,0);
    }
}
```

**Bitshift**

Similar to the bitwise operations, each integer type supports bit shifts. But unlike the other operations, the righthand side operand (how many bits to shift by) must _always_ be a `u8` and need not match the left side operand (the number you are shifting).

Bit shifts can abort if the number of bits to shift by is greater than or equal to `8`, `16`, `32`, `64`, `128` or `256` for `u8`, `u16`, `u32`, `u64`, `u128` and `u256` respectively.

| Syntax | Operation   | Aborts if                                                               |
| ------ | ----------- | ----------------------------------------------------------------------- |
| `<<`   | shift left  | Number of bits to shift by is greater than the size of the integer type |
| `>>`   | shift right | Number of bits to shift by is greater than the size of the integer type |

```rust
module my_addrx::Operations
{
    use std::debug::print;

    fun bitshift_operations(a:u64)
    {
        let ans=a>>2;       print(&ans);
        let ans=a<<2;       print(&ans);

    }

    #[test]
    fun testing()
    {
        bitshift_operations(4);
    }
}
```

**Comparision**

Integer types are the _only_ types in Move that can use the comparison operators. Both arguments need to be of the same type. If you need to compare integers of different types, you will need to cast one of them first.

Comparison operations do not abort.

| Syntax | Operation                |
| ------ | ------------------------ |
| `<`    | less than                |
| `>`    | greater than             |
| `<=`   | less than or equal to    |
| `>=`   | greater than or equal to |

```rust
module my_addrx::Operations
{
    use std::debug::print;

    fun comparison_operations(a:u64,b:u64)
    {
        let ans=a < b;        print(&ans); //always give proper spacing between comparison operator and the operands
        let ans=a > b;        print(&ans);
        let ans=a <= b;       print(&ans);  
        let ans=a >= b;       print(&ans);  

    }

    #[test]
    fun testing()
    {
        comparison_operations(10,14);
    }
}
```

**Equality**

Like all types with drop in Move, all integer types support the "equal" and "not equal" operations. Both arguments need to be of the same type. If you need to compare integers of different types, you will need to cast one of them first.

Equality operations do not abort.

| Syntax | Operation |
| ------ | --------- |
| `==`   | equal     |
| `!=`   | not equal |

```rust
module my_addrx::Operations
{
    use std::debug::print;

    fun comparison_operations(a:u64,b:u64)
    {
        let ans=a == b;        print(&ans);
        let ans=a != b;        print(&ans);
    }

    #[test]
    fun testing()
    {
        comparison_operations(10,14);
    }
}
```

**Casting**

Integer types of one size can be cast to integer types of another size. Integers are the only types in Move that support casting.

Casts _do not_ truncate. Casting will abort if the result is too large for the specified type

| Syntax     | Operation                                            | Aborts if                              |
| ---------- | ---------------------------------------------------- | -------------------------------------- |
| `(e as T)` | Cast integer expression `e` into an integer type `T` | `e` is too large to represent as a `T` |

Here, the type of `e` must be `8`, `16`, `32`, `64`, `128` or `256` and `T` must be `u8`, `u16`, `u32`, `u64`, `u128` or `u256`.

For example:

* `(x as u8)`
* `(y as u16)`
* `(873u16 as u32)`
* `(2u8 as u64)`
* `(1 + 3 as u128)`
* `(4/2 + 12345 as u256)`
