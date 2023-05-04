# Collection

* Basic contract is created for storing `Item` on `Collection` struct in vector form.
* Some of the function like  `start_collection`, `exists_at`, `add_item`, `size` and `destroy` are called publicly in the contract.
* Scripts are also written for this contract.

```rust
module 0x12::Collection{
    use std::vector;
    use std::signer;

    struct Item has store, drop{}

    struct Collection has store, key{
        items:vector<Item>
    }

    public fun start_collection(account: &signer){
        move_to<Collection>(account,Collection{
            items: vector::empty<Item>()
        })
    }

    public fun exists_at(at: address): bool {
        exists<Collection>(at)
    }

    public fun add_item(account: &signer) acquires Collection{
        let addr = signer::address_of(account);
        let collection = borrow_global_mut<Collection>(addr);
        vector::push_back(&mut collection.items,Item{});
    }

    public fun size(account: &signer):u64 acquires Collection{
        let addr = signer::address_of(account);
        let collection = borrow_global_mut<Collection>(addr);
        vector::length(& collection.items)
    }

    public fun destroy(account: &signer) acquires Collection{
        let addr = signer::address_of(account);
        let collection = move_from<Collection>(addr);

        let Collection{items: _} = collection;
    }
}
```

### Script of Collection contract:

```rust
script{
    use 0x12::collection as coll;
    use std::debug;
    use std::signer;

    fun main_resource(account: signer){
        let addr = signer::address_of(&account);
        // OR
        // let addr = @0x63;
        
        coll::destroy(&account);
        coll::start_collection(&account);
        let ea = coll::exists_at(addr);
        debug::print(&ea);
        coll::add_item(&account);
        coll::add_item(&account);
        coll::add_item(&account);
        let lsize = coll::size(&account);
        debug::print(&lsize);
    }
}
```
