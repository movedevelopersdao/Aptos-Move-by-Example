# Token V2

**What is Token V2?**

The Aptos token v2 standard was developed with the following as an improvement on the Aptos Token standard. It has these ideas in mind:

* _Flexibility_ - NFTs are flexible and can be customised to accommodate any creative designs.
* _Composability_ - Multiple NFTs can be easily composed together, such that the final object is greater than the sum of its parts
* _Scalability_ - Greater parallelism between tokens

**Key Difference between Token V2 and other Aptos token standard:**

* Token V2 is an object based token.
* Decoupled token ownership from token data
* Explicit data model for token metadata via adjacent resources
* Extensible framework for tokens

**Comparison between Token V1 and Token V2:**

* Token can be easily extended with custom data and functionalities without requiring any changes in the framework
* Transfers simply a reference update
* Direct transfer is allowed without an opt in
* NFTs can own other NFTs adding easy composability
* Soul bound tokens can be easily supported

**Lets understand Token V2 using a simple example for creating a token**

```rust
module my_addrx::TokenV2
{
    use std::signer;
    use aptos_framework::object; 
    use std::option;
    use aptos_token_objects::token::{Self, Token};
    use aptos_token_objects::royalty;
    use std::string::String;

    public entry fun create_token(account: &signer,collection_name: String, token_name: String, token_description: String, token_uri: String ) {
        
        let signer_address = signer::address_of(account);
        let royalty = royalty::create(1, 10, signer_address);
      
        //minting nft
        let token_constructor_ref = token::create_named_token(
            account,
            collection_name,
            token_description,
            token_name,
            option::some(royalty),
            token_uri
        );
        let obj = object::object_from_constructor_ref<Token>(&token_constructor_ref);
        //Transfering the NFT to the signer of the transaction
        let obj_signer = object::generate_signer(&token_constructor_ref);   
        object::transfer(&obj_signer,obj,signer_address);
    }
}

```

{% hint style="info" %}
For more details [follow this](https://aptos.dev/standards/aptos-token-v2/).&#x20;
{% endhint %}
