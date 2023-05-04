# Control flow and expressions

Control flow statement is a statement that results in a choice being made as to which of two or more paths to follow.&#x20;

Move achieves control flow by using if-else expressions and loops.

**If expression:**

```rust
// syntax for if expression
if(<bool-expression>)
    <expression>
else if(<bool-expression>)
    <expression>
..
..
else 
    <expression>

```

{% hint style="info" %}
For all integer types all the values except 0 are considered true.
{% endhint %}

```rust
module my_addrx::IF_ELSE
{
    use std::debug::print;
    use std::string::utf8;
    
    fun control_flow()
    {
        let val:bool = true;
        if(val)
        {
            print(&utf8(b"If block"));
        }
        else{
            print(&utf8(b"Else block"));
        }; //if is an expression therefore it should be end with a semicolon.
    }
    
    #[test]
    fun testing()
    {
        control_flow();
    }
}
```
