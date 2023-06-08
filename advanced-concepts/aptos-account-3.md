# Object

The _Object model_ allows Move to represent a complex type as a set of resources stored within a single address and offers a rich capability model that allows for fine-grained resource control and ownership management.

**Properties of Object model**

* Simplified storage interface that supports a heterogeneous collection of resources to be stored together. This enables data types to share a common core data layer (e.g., tokens), while having richer extensions (e.g., concert ticket, sword).
* Globally accessible data and ownership model that enables creators and developers to dictate the application and lifetime of data.
* Extensible programming model that supports individualization of user applications that leverage the core framework including tokens.
* Support emitting events directly, thus improving discoverability of events associated with objects.
* Considerate of the underlying system by leveraging resource groups for gas efficiency, avoiding costly deserialization and serialization costs, and supporting deletability.

{% hint style="info" %}
Object is a core primitive in Aptos Move and created via the object module at 0x1::object
{% endhint %}

```rust
module my_addrx::MyFriends
{
    use std::vector;
    use aptos_std::object::{Self,Object}; 
    use std::signer;
    use aptos_framework::account;

    struct MyFriends has key
    {
        friends: vector<vector<u8>>,
    }

    public entry fun create_friends(caller: &signer, list:vector<vector<u8>> ) : Object<MyFriends>
    {
        let myfriend_constructor_ref = object::create_object_from_account(caller); 
        let myfriend_signer = object::generate_signer(&myfriend_constructor_ref);   
        move_to(&myfriend_signer, MyFriends{ friends:list });  
        let obj = object::object_from_constructor_ref<MyFriends>(&myfriend_constructor_ref);
        return obj
    }

    public entry fun transferring_of_ownership(from: &signer,to: address,obj: Object<MyFriends>) : address
    {
        object::transfer(from,obj,to); //transferring ownership of the object
        let new_owner_of_the_object = object::owner(obj); // ownership is tracked on the object itself
        return new_owner_of_the_object
    }

    #[test(owner = @0x123)]
    public entry fun test_flow(owner: signer)  
    {
        account::create_account_for_test(signer::address_of(&owner));
        
        let list = vector::empty<vector<u8>>();
        vector::push_back(&mut list, b"John");
        vector::push_back(&mut list, b"Harry");
        vector::push_back(&mut list, b"Gwen");   
        let obj = create_friends(&owner,list);
        
        assert!(signer::address_of(&owner) == @0x123,1);
         
        //transferring the ownership of the object from the owner account to 0x345 account
        
        let new_owner_address = transferring_of_ownership(&owner,@0x345,obj);
         
        assert!(new_owner_address == @0x345,1);
    }
    
}
```
