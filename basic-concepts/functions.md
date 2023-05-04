# Functions

```rust
visibility fun function_name(arg1:data_type,arg2:data_type ,..) : (data_type,data_type,..)
{
    //your code
}
```

Below code helps us understand that how we can pass on arguments and return values from functions.

```rust
module my_addrx::Functions
{
    use std::debug::print;
    use std::string::utf8;

    fun greet()
    {
        print(&utf8(b"Functions in move"));
    }


    fun sqaure(num : u64)
    {
        let s=num*num;
        print(&s);
    }
    
    //function returning maximum of two numbers
    fun max(num1 : u64,num2 : u64): u64{
        if(num1 < num2)
            return num2
        else    
            return num1
    }


    //In move a function can return multiple values
    fun is_even(num : u64) : (u64,bool)
    {
        if(num % 2 == 0)
            return (num,true)
        else
            return (num,false)
    }


    #[test]
    fun testing()
    {
        greet();
        sqaure(10);

        let m=max(53,10);
        print(&m);

        let (v1,v2) = is_even(4);
        print(&v1);
        print(&v2);

    }
}
```
