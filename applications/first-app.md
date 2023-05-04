# First App

Our first application will be quite simple yet quite important to understand how we can make digital assets(Resources) in move and store on Aptos blockchain.

In this module we will make a resource `Message` which will contain a message which the user will be able to store and update on blockchain.

**Test:**

Since our test runs outside of an account scope, we need to _create_ accounts to use in our test. The `#[test]` annotation gives us the option to declare those accounts. We use an `admin` account and set it to a random account address (`@0x123`). The function accepts this signer (account) and creates it by using a built-in function to create an account for test.

```rust
module my_addrx::Message
{
    use std::string::{String,Self};
    use std::signer;
    use aptos_framework::account;
    
    struct Message has key
    {
        my_message : String
    }

    public entry fun  create_message(account: &signer, msg: String)  acquires Message{
        let signer_address = signer::address_of(account);
        
        if(!exists<Message>(signer_address))  //If the resource does not exits corresponding to a given address
        {
            let message = Message {
                my_message : msg             //first create a resouce
            };
            move_to(account,message);        //move that resouce to the account
        }

        else                                 //If the resource exits corresponding to a given address
        {
            let message = borrow_global_mut<Message>(signer_address); //get the resouce 
            message.my_message=msg;                                   //update the resouce
        }
        
    }


    #[test(admin = @0x123)]
    public entry fun test_flow(admin: signer) acquires Message 
    {
        account::create_account_for_test(signer::address_of(&admin));
        create_message(&admin,string::utf8(b"This is my message"));
        create_message(&admin,string::utf8(b"I changed my message"));
        
        let message=borrow_global<Message>(signer::address_of(&admin));
        assert!(message.my_message==string::utf8(b"I changed my message"),10);
    }

}
```
