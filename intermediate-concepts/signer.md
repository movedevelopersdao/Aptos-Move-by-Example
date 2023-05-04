# Signer

A `signer` is a type that represents the authorization and control of a resource or asset on the blockchain. The signer type is used to indicate which account or entity is responsible for executing a particular transaction or operation on the blockchain.

You can think of the native implementation as being:

```rust
struct signer has drop { a: address }
```

Signer values are special because they cannot be created via literals or instructions--only by the Move VM. Before the VM runs a script with parameters of type `signer`, it will automatically create `signer` values and pass them into the script:

```rust
script {
    use std::signer;
    fun main(s: signer) {
        assert!(signer::address_of(&s) == @my_addrx, 0);
    }
}
```

**`signer` operations:**

The `std::signer` standard library module provides two utility functions over `signer` values:

* `signer::address_of(&signer): address` - Return the `address` wrapped by this `&signer`.
* `signer::borrow_address(&signer): &address` - Return a reference to the `address` wrapped by this `&signer`.

```rust
module my_addrx::MyResource
{
    use std::signer; 
    
    struct MyResource has key
    {
        value:u64
    }

    public entry fun increase_value_by_one(account: &signer) acquires MyResource {
        
        let signer_address = signer::address_of(account); 
        
        let myresource = borrow_global_mut<MyResource>(signer_address);
        myresource.value=myresource.value+1;
    }
}

```

**Ownership:**

Unlike simple scalar values, `signer` values are not copyable, meaning they cannot be copied(from any operation whether it be through an explicit `copy` instruction or through a `dereference *`.
