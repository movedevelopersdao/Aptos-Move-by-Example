# Aptos account

An account on the Aptos blockchain contains blockchain assets. These assets, for example, coins and NFTs, are by nature scarce and must be access-controlled. Any such asset is represented in the blockchain account as a **resource**. A resource is a Move language primitive that emphasizes access control and scarcity in its representation. However, a resource can also be used to represent other on-chain capabilities, identifying information, and access control.

Each account on the Aptos blockchain is identified by a 32-byte account address. An account can store data, and the account stores this data in resources. The initial resource is the account data itself (authentication key and sequence number). Additional resources like currency or NFTs are added after creating the account. And you can employ the [Aptos Name Service](https://aptos.dev/integration/aptos-names-service-package) at [www.aptosnames.com](https://www.aptosnames.com/) to secure .apt domains for key accounts to make them memorable and unique.

Different from other blockchains where accounts and addresses are implicit, accounts on Aptos are explicit and need to be created before they can hold resources and modules. The account can be created explicitly or implicitly by transferring Aptos tokens (APT) there. See the [Creating an account](https://aptos.dev/concepts/accounts#creating-an-account) section for more details. In a way, this is similar to other chains where an address needs to be sent funds for gas before it can send transactions.

Explicit accounts allow first-class features that are not available on other networks such as:

* Rotating authentication key. The account's authentication key can be changed to be controlled via a different private key. This is similar to changing passwords in the web2 world.
* Native multisig support. Accounts on Aptos support multi-ed25519 which allows for a multisig authentication scheme when constructing the authentication key. In the future, more authentication schemes can be introduced easily.
* More integration with the rest of ecosystem to bring in features such as profiles, domain names, etc. that can be seamlessly integrated with the Aptos account.

There are two types of accounts in Aptos:

* _Standard account_ - This is a typical account corresponding to an address with a corresponding pair of public/private keys.
* _Resource account_ - An autonomous account without a corresponding private key used by developers to store resources or publish modules on chain.

Account addresses are 32-bytes. They are usually shown as 64 hex characters, with each hex character a nibble. Sometimes the address is prefixed with a 0x.

```rust
Alice: 0xeeff357ea5c1a4e7bc11b2b17ff2dc2dcca69750bfef1e1ebcaccf8c8018175b
Bob: 0x19aadeca9388e009d136245b9a67423f3eee242b03142849eb4f81a4a409e59c
```

If there are leading 0s, they may be excluded:

```rust
Dan: 0000357ea5c1a4e7bc11b2b17ff2dc2dcca69750bfef1e1ebcaccf8c8018175b 
Dan: 0x0000357ea5c1a4e7bc11b2b17ff2dc2dcca69750bfef1e1ebcaccf8c8018175b
Dan: 0x357ea5c1a4e7bc11b2b17ff2dc2dcca69750bfef1e1ebcaccf8c8018175b
```

**Account identifiers**

Currently, Aptos supports only a single, unified identifier for an account. Accounts on Aptos are universally represented as a 32-byte hex string. A hex string shorter than 32-bytes is also valid; in those scenarios, the hex string can be padded with leading zeroes, e.g., 0x1 => 0x0000000000000...01.

**Creating Aptos account**

When a user requests to create an account, for example by using the Aptos SDK, the following cryptographic steps are executed:

* Start by generating a new private key, public key pair.
* From the user, get the preferred signature scheme for the account: If the account should use a single signature or if it should require multiple signatures for signing a transaction.
* Combine the public key with the user's signature scheme to generate a 32-byte authentication key.
* Initialize the account sequence number to 0. Both the authentication key and the sequence number are stored in the account as an initial account resource.
* Create the 32-byte account address from the initial authentication key.

From now on, the user should use the private key for signing the transactions with this account.

**Access control with signer**

The sender of a transaction is represented by a signer. When a function in a Move module takes `signer` as an argument, then the Aptos Move VM translates the identity of the account that signed the transaction into a signer in a Move module entry point. See the below Move example code with `signer` in the `initialize` and `withdraw` functions. When a `signer` is not specified in a function, for example, the below `deposit` function, then no access controls exist for this function:

```rust
module Test::Coin {
  struct Coin has key { amount: u64 }

  public fun initialize(account: &signer) {
    move_to(account, Coin { amount: 1000 });
  }

  public fun withdraw(account: &signer, amount: u64): Coin acquires Coin {
    let balance = &mut borrow_global_mut<Coin>(Signer::address_of(account)).amount;
    *balance = *balance - amount;
    Coin { amount }
  }

  public fun deposit(account: address, coin: Coin) acquires Coin {
      let balance = &mut borrow_global_mut<Coin>(account).amount;
      *balance = *balance + coin.amount;
      Coin { amount: _ } = coin;
  }
}
```

**Resource accounts**

A resource account is a developer feature used to manage resources independent of an account managed by a user, specifically publishing modules and automatically signing for transactions.

For example, a developer may use a resource account to manage an account for module publishing, say managing a contract. The contract itself does not require a signer post initialization. A resource account gives you the means for the module to provide a signer to other modules and sign transactions on behalf of the module.

Typically, a resource account is used for two main purposes:

* Store and isolate resources; a module creates a resource account just to host specific resources.
* Publish module as a standalone (resource) account, a building block in a decentralized design where no private keys can control the resource account. The ownership (SignerCap) can be kept in another module, such as governance.
