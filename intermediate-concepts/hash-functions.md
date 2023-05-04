# Hash functions

**`std::hash`**

`sha2_256` and `sha3_256` are available in `std::hash`.

```rust
module my_addrx::Hashing
{
    use std::hash;
    use std::bcs;
    use std::debug::print;
    
    fun hashing_in_move():vector<u8>
    {
        let x:vector<u8> = bcs::to_bytes<u64>(&10);
        let h = hash::sha2_256(x); 
        h 
    }

    #[test]
    fun testing()
    {
        let tmp=hashing_in_move();
        print(&tmp);
    }
}
```

**`std::aptos_hash`**

`keccak256` ,`sha2_512` ,`sha3_512` ,`ripemd160`  are available in `std::aptos_hash`.

```rust
module std::Hashing
{
    use std::aptos_hash;
    use std::bcs;
    use std::debug::print;
    
    fun hashing_in_move():vector<u8>
    {
        let x:vector<u8> = bcs::to_bytes<u64>(&10);
        let h = aptos_hash::keccak256(x); 
        h 
    }

    #[test]
    fun testing()
    {
        let tmp=hashing_in_move();
        print(&tmp);
    }
}
```
