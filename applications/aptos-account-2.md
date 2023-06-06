# Dutch Auction

```rust
module my_addrx::Dutch_Auction { 

    use 0x1::signer;
    use std::timestamp;
    /// Error codes
    const STARTING_PRICE_IS_LESS: u64 = 0;
    const E_NOT_ENOUGH_COINS: u64 = 101;
    const TIME_ERROR: u64 = 2;
    const EINVALID_REWARD_AMOUNT: u64 = 3;
    const LESS_PRICE: u64 = 4;
    const EINSUFFICIENT_BALANCE: u64 = 5;

    struct Discounting has store,key{
        seller: address,
        startingPrice: u64,
        startAt: u64,
        expiresAt: u64,
        discountRate: u64
    }

    public fun assert_is_owner(addr: address) {
        assert!(addr == @my_addrx, 0);
    }

    public fun assert_is_initialized(addr: address) {
        assert!(exists<Discounting>(addr), 1);
    }

    public fun assert_uninitialized(addr: address) {
        assert!(!exists<Discounting>(addr), 3);
    }

    public fun initialize(acc: &signer, _startingPrice:u64, _discountRate:u64 )  {
        let addr = signer::address_of(acc);

        assert_is_owner(addr);
        assert_uninitialized(addr);
        let currenttime = timestamp::now_seconds();
        let duration = 604800; //7 days//

        let discount = Discounting {
            seller: addr,
            startingPrice: _startingPrice,
            startAt: currenttime,
            expiresAt: currenttime + duration,
            discountRate: _discountRate
        };
        move_to(acc, discount);

        assert!(_startingPrice >= _discountRate * duration, STARTING_PRICE_IS_LESS);
    }

    public fun getPrice(acc: &signer, store_addr: address) :u64 acquires Discounting {
        let _addr = signer::address_of(acc);
        assert_uninitialized(store_addr);

        let currenttime = timestamp::now_seconds();
        let b_store = borrow_global_mut<Discounting>(store_addr);

        let timeElapsed = currenttime - b_store.startAt;
        let discount = b_store.discountRate * timeElapsed;
        let value = b_store.startingPrice - discount;
        return value
    }
}
```
