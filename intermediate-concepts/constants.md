# Constants

Constants are a way of giving a name to shared, static values inside of a `module` or `script`. In Move, constants are declared using the `const` keyword.

The constant's must be known at compilation. The constant's value is stored in the compiled module or script. And each time the constant is used, a new copy of that value is made.

**Syntax:**

```rust
const <NAME>: <TYPE> = <EXPRESSION>;
```

```rust
module my_addrx::Constants
{
    use std::debug::print;

    //Some Examples
    const X:u64=10;
    const Y:address=@my_addrx;
    const Z:bool=false;

    fun constants()
    {
        print(&X);
        print(&Y);
        print(&Z);
        
    }

    #[test]
    fun testing()
    {
        constants();
    }
}
```

**Naming:**

Constants must start with a capital letter `A` to `Z`. After the first letter, constant names can contain underscores `_`, letters `a` to `z`, letters `A` to `Z`, or digits `0` to `9`.

```rust
//Valid
const Foo:u64=123;
const Flag:bool=true; 
const My_Addrx:address=@my_addrx;

//Invalid;
const x:u8=10;
const flag:bool=false;
const my_addrx:address=@my_addrx;
```

**Some other important points:**

* Constants are limited to the primitive types `bool`, `u8`, `u16`, `u32`, `u64`, `u128`, `u256`, `address`, and `vector<u8>`.
* Constant names should be upper camel case and begin with an `E` if they represent error codes (e.g., `EIndexOutOfBounds`) and upper snake case if they represent a non-error value (e.g., `MIN_STAKE`).
* `public` constants are not currently supported. `const` values can be used only in the declaring module.
* Constant value cannot be accessed outside its module or script scope.
