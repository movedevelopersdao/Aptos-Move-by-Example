# Struct and its Abilities

Struct is a way of defining custom type in move. It can be described as a simple key-value storage where key is a name of property and value is what's stored.

Syntax:

```rust
struct NAME has Abilities
{
    FIELD1 : TYPE1,
    FIELD2 : TYPE2,
    ...
}
```

**Struct** can have up to 4 abilities which define how values of this type can be used, dropped or stored.

* **Copy** - value can be _copied_ (or cloned by value).
* **Drop** - value can be _dropped_ by the end of scope.
* **Key** - value can be _used as a key_ for global storage operations.
* **Store** - value can be _stored_ inside global storage.

{% hint style="info" %}
Abilities for Primitive types like _integers_, _vector_, _addresses_ and _boolean_  are pre-defined and unchangeable : _copy_, _drop_ and _store_.
{% endhint %}

```rust
module my_addrx::Application {
	use std::vector;
        use std::debug;
	use std::string::{String,utf8};

	struct Users has key,drop {
		list_of_users: vector<User>    //storing the list of the users
	}

	struct User has store,drop,copy {
		name:String,                   //information required for a typical user
		age:u8
	}

        //creating a user by adding the user to the existing list and returning the user
	public fun create_user(newUser: User,users: &mut Users): User{
		vector::push_back(&mut users.list_of_users,newUser);
		return newUser
	}
	
	#[test]
	fun test_create_friend(){
		let user1 = User {
			name:utf8(b"Tony"),
			age:50,
		};
		
        let users = Users{
			list_of_users: vector::empty<User>()
		};

		let createdUser = create_user(user1,&mut users);
        debug::print(&users);
        assert!(createdUser.name == utf8(b"Tony"),0);
	}
}
```
