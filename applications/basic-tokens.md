# Basic Tokens

**Contract logic:**

Basic Tokens provide functionalities to

* Should called `create_balance` function by signer.
* Before calling `deposit`, `withdraw` and `transfer` function of funds by users.

Here is the interface for Basic Token.

```rust
module coin_addrx::BasicCoins{

    use std::signer;

    struct Coins has store{ val : u64 }

    struct Balance has key{
        coins:Coins
    }

    /// Error codes
    const ERR_BALANCE_NOT_EXISTS: u64 = 101;
    const ERR_BALANCE_EXISTS: u64 = 102;
    const EINSUFFICIENT_BALANCE: u64 = 1;
    const EALREADY_HAS_BALANCE: u64 = 2;
    const EEQUAL_ADDR: u64 = 4;

    /// Mint `amount` called val tokens on `contract`. 
    public fun mint(val: u64):Coins{
        let new_coin = Coins{val};
        new_coin
    }

     /// Burn `amount` tokens from `contract`.
    public fun burn(coin : Coins){
        let Coins{val: _ } = coin;
    }

    public fun create_balancce(acc: &signer){
        let acc_addr = signer::address_of(acc);

        assert!(!balance_exists(acc_addr),ERR_BALANCE_EXISTS);

        let zero_coins = Coins{val:0};
        move_to(acc,Balance{coins : zero_coins});
    }

    public fun balance_exists(acc_addr: address):bool{
        exists<Balance>(acc_addr)
    }

     public fun balance(owner: address): u64 acquires Balance {
        borrow_global<Balance>(owner).coins.val
    }

    public fun deposit(acc_addr: address,coins:Coins) acquires Balance{
        let balance = balance(acc_addr);
        assert!(balance_exists(acc_addr),ERR_BALANCE_NOT_EXISTS);
        let balance_ref = &mut borrow_global_mut<Balance>(acc_addr).coins.val;
        let Coins{val} = coins;
        *balance_ref = balance + val;
    }


    public fun withdraw(acc: address,value : u64) : Coins acquires Balance{
        assert!(balance_exists(acc),ERR_BALANCE_NOT_EXISTS);
        let balance = balance(acc);
        assert!(balance >= value, EINSUFFICIENT_BALANCE);
        let balance_ref = &mut borrow_global_mut<Balance>(acc).coins.val;
        *balance_ref = balance - value;
         Coins{ val: value }
    }
   /// Transfers `amount` of tokens from `from` to `to`.
    public fun transfer(from: &signer, to: address, amount: u64) acquires Balance {
        let from_addr = signer::address_of(from);
        assert!(from_addr != to, EEQUAL_ADDR);
        let check = withdraw(from_addr, amount);
        deposit(to, check);
    }
    

    #[test(acc=@0x123)]
    fun test_use_some_coins(acc: signer) acquires Balance{
        let acc_addr = signer::address_of(&acc);
        let coins_10 = mint(10);
        create_balancce(&acc);
        deposit(acc_addr,coins_10);
        assert!(balance(acc_addr)==10,EINSUFFICIENT_BALANCE);

        let coins_5 = withdraw(acc_addr, 5);
        assert!(balance(acc_addr)==5,EALREADY_HAS_BALANCE);

        burn(coins_5);
    }

}
```
