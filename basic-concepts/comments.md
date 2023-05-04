# Comments

Comments are non executable piece of code that a programmer can add to make their code readable.

```rust
module my_addrx::Comments
{
    use std::debug::print;
    
    fun printing()
    {
        //This is a line comment

        /*
            This 
            is a
            block
            comment
        */
        
        let a=/*You can put block comment here*/12345;
        print(&/*Even here*/a);
        
    }

    #[test]
    
    fun testing()
    {
        printing();
    }
}
```
