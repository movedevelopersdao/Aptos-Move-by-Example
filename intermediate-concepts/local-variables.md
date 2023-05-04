# Local variables

Local variables in Move are lexically (statically) scoped. New variables are introduced with the keyword `let`, which will shadow any previous local with the same name. Locals are mutable and can be updated both directly and via a mutable reference.

**Syntax:**

```rust
let <VARIABLE>:TYPE;  //explicit type annotation
let <VARIABLE>=<EXPRESSION>;  //implicit type annotation
```

```rust
module my_addrx::Variable
{
    fun local_variables()
    {
        let b:u8;
        let c=false;
        let d=b"hello world";
        let e:u64=10000;
        let f:address = @my_addrx;
    }
}
```



**Variables must be assigned before use**

Move's type system prevents a local variable from being used before it has been assigned.

```rust
let a;
let b=a+10; //ERROR
```

**Valid variable names**

Variable names can contain underscores `_`, letters `a` to `z`, letters `A` to `Z`, and digits `0` to `9`. Variable names must start with either an underscore `_` or a letter `a` through `z`. They _cannot_ start with uppercase letters.

```rust
// all valid
let x = 1;
let _x = 1;
let _A = 1;
let x0 = 1;
let xA = 1;
let foobar_123 = 1;

// all invalid
let X = 1; // ERROR!
let Foo = 1; // ERROR!
```



**Multiple declarations with tuples**

`let` can introduce more than one local at a time using tuples. The locals declared inside the parenthesis are initialized to the corresponding values from the tuple.

```rust
//Valid
let (x,y,z) = (1,@0x1,false);
//Invalid
let (p,q) = (1,2,3,4);
```

**Underscore for unused variables**

In Move every variable must be used (otherwise your code won't compile), hence you can't initialize one and leave it untouched. Though you have one way to mark variable as _intentionally unused_ - by using underscore `_`.

```rust
module my_addrx::Variable
{
    fun local_variables()
    {
        let a=1;   //compiling this module will result in error as "a" is not being used.
    }
}
```

```rust
module my_addrx::Variable
{
    fun local_variables()
    {
        let _=1; //compiling this module will not result in error
        
        let b=10;
        _=b;    //This is called shadowing
    }
}
```
