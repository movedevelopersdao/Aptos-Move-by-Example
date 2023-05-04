# Strings

Move does not have native type for strings, therefore in order to use strings we have to include string module or we can use `vector<u8>` for storing byte string.

```rust
//using string module
module my_addrx::Strings{
    use std::debug;
    use std::string::{String,utf8};
    
    fun greeting():String {
        let greet:String = utf8(b"Welcome to Aptos Move by Example");
        return greet
    }


    #[test]
    fun testing(){
        let greet=greeting();
        debug::print(&greet);
    } 
}

```

```rust
//using vector<u8> for representing byte string
module my_addrx::Strings{
    use std::debug;
    use std::string::utf8;
    
    fun greeting():vector<u8> {
        let greet:vector<u8> = b"Welcome to Aptos move by examples"; 
        return greet
    }


    #[test]
    fun testing(){
        let greet=greeting();
        debug::print(&greet); //It will print byte string literal form 
        debug::print(&utf8(greet)); 
    } 
}
```
