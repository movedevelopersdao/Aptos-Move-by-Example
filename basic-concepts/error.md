# Error

You can throw an error by calling  `assert` and `abort`.

**Abort:**

`abort error_code //error code is of type u64`

```rust
module my_addrx::Errors
{
    use std::debug::print;
    use std::string::utf8;

    fun isEven(num:u64)
    {
        if(num%2==0)
        {    
            print(&utf8(b"No Error as the Number is Even"));
        }
        else    
        {    
            abort 11 //throwing error as the given number is not even
        }
    }

    #[test]
    fun testing()
    {
        isEven(3);
    }
}
```

**Assert:**

`assert!(expression: bool, error_code: u64)`

```rust
module my_addrx::Errors
{

    fun isEven(num:u64)
    {
        assert!(num%2==0,11);
    }

    #[test]
    fun testing()
    {
        isEven(3);
    }
}
```
