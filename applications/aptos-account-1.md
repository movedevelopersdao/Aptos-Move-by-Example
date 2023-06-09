# English Auction

* The English auction, also known as an open ascending price auction, is a widely recognized auction format.
* The auction starts with a low initial price and gradually increases as bidders place higher bids.
* It encourages competition among participants and is commonly used for unique items or high-value goods.
* The transparent nature of the auction allows bidders to gauge the demand and value of the item, leading to intense bidding wars.
* The auction format has seamlessly transitioned into the digital era with online platforms, enabling a larger audience to participate.

```rust
module my_addrx::English_Auction{ 

    use 0x1::vector;
    use 0x1::signer;
    use 0x1::simple_map::{Self, SimpleMap};
    /// Error codes
    const YOU_ARE_WINNER: u64 = 1;
    const EINSUFFICIENT_BALANCE: u64 = 2;

    struct BetAddrList has key {
        bet_addr_list: SimpleMap<address, u64>,
        b_list: vector<address>,
        winner: address
    }

    public fun assert_is_owner(addr: address) {
        assert!(addr == @my_addrx, 0);
    }

    public fun assert_is_initialized(addr: address) {
        assert!(exists<BetAddrList>(addr), 1);
    }

    public fun assert_uninitialized(addr: address) {
        assert!(!exists<BetAddrList>(addr), 3);
    }

    public fun assert_contains_key(map: &SimpleMap<address, u64>, addr: &address) {
        assert!(simple_map::contains_key(map, addr), 2);
    }

    public fun assert_not_contains_key(map: &SimpleMap<address, u64>, addr: &address) {
        assert!(!simple_map::contains_key(map, addr), 4);
    }

    public entry fun initialize_with_bet(acc: &signer, amount:u64) acquires BetAddrList {
        let addr = signer::address_of(acc);
        let balance = my_addrx::BasicTokens::balance_of(addr);
        assert!(balance >= amount, EINSUFFICIENT_BALANCE);
        
        assert_is_owner(addr);
        assert_uninitialized(addr);

        let b_store = BetAddrList{
            bet_addr_list:simple_map::create(),
            b_list: vector::empty<address>(),
            winner: @0x0,
            };

            move_to(acc, b_store);

        let b_store = borrow_global_mut<BetAddrList>(addr);
        simple_map::add(&mut b_store.bet_addr_list, addr, amount);
        vector::push_back(&mut b_store.b_list, addr);
        my_addrx::BasicTokens::withdraw(addr, amount);
    }

    public entry fun placeBet(acc: &signer, store_addr: address, amount:u64) acquires BetAddrList {
        let b_addr = signer::address_of(acc);
        let balance = my_addrx::BasicTokens::balance_of(b_addr);
        assert!(balance >= amount, EINSUFFICIENT_BALANCE);

        let b_store = borrow_global_mut<BetAddrList>(store_addr);
        assert!(b_store.winner == @0x0, 5);
        
            let balance = my_addrx::BasicTokens::balance_of(b_addr);
            assert!(balance >= amount, EINSUFFICIENT_BALANCE);

            simple_map::add(&mut b_store.bet_addr_list, b_addr, amount);
            vector::push_back(&mut b_store.b_list, b_addr);
            my_addrx::BasicTokens::withdraw(b_addr, amount);
    }

    public entry fun declare_winner(acc: &signer) acquires BetAddrList {
        let addr = signer::address_of(acc);
        assert_is_owner(addr);
        assert_is_initialized(addr);

        let b_store = borrow_global_mut<BetAddrList>(addr);
        assert!(b_store.winner == @0x0, 5);

        let total_betters = vector::length(&b_store.b_list);

        let i = 0;
        let winner: address = @0x0;
        let highest_bet: u64 = 0;

        while (i < total_betters) {
            let better = *vector::borrow(&b_store.b_list, (i as u64));
            let bet_amount = simple_map::borrow(&b_store.bet_addr_list, &better);

            if(highest_bet < *bet_amount) {
                highest_bet = *bet_amount;
                winner = better;
            };
            i = i + 1;
        };

        b_store.winner = winner;
    }

    public fun claim_your_amount(acc_own: &signer, store_addr: address) acquires BetAddrList{
        let addr = signer::address_of(acc_own);
        
        let b_store = borrow_global_mut<BetAddrList>(store_addr);
        let bet_amount = simple_map::borrow_mut(&mut b_store.bet_addr_list, &addr);
        
        
        assert!(b_store.winner != addr, 5);
        assert!(*bet_amount != 0, 6);

        my_addrx::BasicTokens::withdraw(addr, *bet_amount);
        *bet_amount = 0;
    }


    #[test(admin = @my_addrx,alice=@0x11,bob=@0x2)]
    public entry fun test_betting(admin: signer,alice : signer, bob : signer)  acquires BetAddrList{
        let better = account::create_account_for_test(signer::address_of(&admin));
        let better2 = account::create_account_for_test(signer::address_of(&alice));
        let better3 = account::create_account_for_test(signer::address_of(&bob));

        // Publish balance for Alice and Bob
        my_addrx::BasicTokens::publish_balance(&admin);
        my_addrx::BasicTokens::publish_balance(&alice);
        my_addrx::BasicTokens::publish_balance(&bob);

        // Mint some tokens to Alice
        my_addrx::BasicTokens::mint<my_addrx::BasicTokens::Coin>(signer::address_of(&admin), 1000);
        my_addrx::BasicTokens::mint<my_addrx::BasicTokens::Coin>(signer::address_of(&alice), 1000);
        my_addrx::BasicTokens::mint<my_addrx::BasicTokens::Coin>(signer::address_of(&bob), 1000);

        initialize_with_bet(&better,100);
        placeBet(&better2, signer::address_of(&admin),200);
        placeBet(&better3, signer::address_of(&admin),300);

        let b_store = &borrow_global<BetAddrList>(signer::address_of(&admin)).bet_addr_list;
        assert_contains_key(b_store, &signer::address_of(&better));
        assert_contains_key(b_store, &signer::address_of(&better2));
        assert_contains_key(b_store, &signer::address_of(&better3));

        declare_winner(&admin);
    }
}

module my_addrx::BasicTokens{
    use std::error;
    use std::signer;

    /// Error codes
    const ENOT_MODULE_OWNER: u64 = 0;
    const EINSUFFICIENT_BALANCE: u64 = 1;
    const EALREADY_HAS_BALANCE: u64 = 2;
    const EALREADY_INITIALIZED: u64 = 3;
    const EEQUAL_ADDR: u64 = 4;

    struct Coin has store,drop {
        value: u64
    }

    struct Balance has key {
        coin: Coin
    }

    public fun createCoin(v:u64): Coin
    {
        let coin = Coin {
            value:v
        };
        return coin
    }


    public fun publish_balance(account: &signer) {
        let empty_coin = Coin { value: 0 };
        assert!(!exists<Balance>(signer::address_of(account)), error::already_exists(EALREADY_HAS_BALANCE));
        move_to(account, Balance { coin:  empty_coin });
    }

    public fun mint<CoinType: drop>(mint_addr: address, amount: u64) acquires Balance {
        deposit(mint_addr, Coin{ value: amount });
    }

    public fun burn(burn_addr: address, amount: u64) acquires Balance {
        let Coin { value: _ } = withdraw(burn_addr, amount);
    }

    public fun balance_of(owner: address): u64 acquires Balance {
        borrow_global<Balance>(owner).coin.value
    }


    public fun transfer(from: &signer, to: address, amount: u64) acquires Balance {
        let from_addr = signer::address_of(from);
        assert!(from_addr != to, EEQUAL_ADDR);
        let check = withdraw(from_addr, amount);
        deposit(to, check);
    }

    public fun withdraw(addr: address, amount: u64) : Coin acquires Balance {
        let balance = balance_of(addr);
        assert!(balance >= amount, EINSUFFICIENT_BALANCE);
        let balance_ref = &mut borrow_global_mut<Balance>(addr).coin.value;
        *balance_ref = balance - amount;
        Coin { value: amount }
    }

    public fun deposit(addr: address, check: Coin) acquires Balance{
        let balance = balance_of(addr);
        let balance_ref = &mut borrow_global_mut<Balance>(addr).coin.value;
        let Coin { value } = check;
        *balance_ref = balance + value;
    }
}
```
