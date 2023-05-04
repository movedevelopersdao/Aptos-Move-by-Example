# Resources

Move is designed as an object-oriented language to write smart contracts or programs with safe resource management. Assets are defined as a “resource”, which can be moved between accounts, but which cannot be double-spent or duplicated.

This makes it very easy to write error-free code, in contrast to Solidity, where transfers of assets must be specified manually, increasing the likelihood of writing faulty code.

**Resource** is meant to be a perfect type for storing _digital assets_, to achieve that it must to be non-copyable and non-droppable. At the same time it must be storable and transferable between accounts.

**Defining a Resource:**

```rust
struct ResourceName has key, store {
        FIELD: TYPE
}
```

{% hint style="info" %}
There's a convention to call main resource of the module after module name.
{% endhint %}

```rust
module my_addrx::Basket
{
    use std::string::String;
    struct Basket has key,store   //Basket is the resource containing list of fruits in the basket
    {
        list_of_fruits:vector<Fruit>
    }

    struct Fruit has store,drop,copy
    {
        name:String,
        calories:u8
    }
}
```
