# Code of Swapping Protocol

```rust
module swap_account::SimplpSwap{
    
    use aptos_framework::coin::{Self,Coin, MintCapability, BurnCapability};
    use std::string;
    use std::string::String;
    use std::option;
    use swap_account::Math::{sqrt,min};
    use std::signer::address_of;
    use swap_account::Math;

    const MINIMUM_LIQUIDITY: u64 = 1000;

    struct LP<phantom X,phantom Y>{}

    struct Pair<phantom X, phantom Y> has key{
        x_coin:Coin<X>,
        y_coin:Coin<Y>,
        lp_locked:Coin<LP<X,Y>>,
        lp_mint:MintCapability<LP<X,Y>>,
        lp_burn:BurnCapability<LP<X,Y>>,
    }

    //NB,USDT-> LP-NB-USDT
    public fun generate_lp_name_symbol<X, Y>():String {
        let lp_name_symbol = string::utf8(b"");
        string::append_utf8(&mut lp_name_symbol,b"LP");
        string::append_utf8(&mut lp_name_symbol,b"-");
        string::append(&mut lp_name_symbol,coin::symbol<X>());
        string::append_utf8(&mut lp_name_symbol,b"-");
        string::append(&mut lp_name_symbol,coin::symbol<Y>());
        lp_name_symbol
    }

    public entry fun create_pool<X,Y>(sender:&signer) {
        assert!(!pair_exist<X,Y>(@swap_account),1000);

        let lp_name_symbol = generate_lp_name_symbol<X,Y>();

        let (lp_burn,lp_freeze,lp_mint) = coin::initialize<LP<X,Y>>(
            sender,
            lp_name_symbol,
            lp_name_symbol,
            6,
            true
        );
        coin::destroy_freeze_cap(lp_freeze);

        move_to(sender,Pair<X,Y>{
            x_coin:coin::zero<X>(),
            y_coin:coin::zero<Y>(),
            lp_locked:coin::zero<LP<X,Y>>(),
            lp_mint,
            lp_burn,
        })
    }

    /*
    Liquidity added for the first time:
        sqrt(x_amount * y_amount) - MINIMUM_LIQUIDITY(1000)
    Add liquidity:
         min(x_amount / x_reserve * lp_total_supply , y_amount / y_reserve * lp_total_supply)
    */
    public entry fun add_liquiduty<X,Y>(sneder:&signer,x_amount:u64,y_amount:u64) acquires Pair{
        //make sure lp exists
        assert!(exists<Pair<X,Y>>(@swap_account),1000);

        let pair = borrow_global_mut<Pair<X,Y>>(@swap_account);

        let x_amount = (x_amount as u128);
        let y_amount = (y_amount as u128);

        let x_reserve = (coin::value(&pair.x_coin) as u128);
        let y_reserve = (coin::value(&pair.y_coin) as u128);

        let y_amount_optimal = quote(x_amount,x_reserve,y_reserve);

        if (y_amount_optimal <= y_amount) {
            y_amount = y_amount_optimal;
        }else{
            let x_amount_optimal = quote(y_amount,y_reserve,x_reserve);
            x_amount = x_amount_optimal;
        };

        //EINSUFFICIENT_BALANCE
        let x_amount_coin = coin::withdraw<X>(sneder,(x_amount as u64));
        let y_amount_coin = coin::withdraw<Y>(sneder,(y_amount as u64));

        // deposit tokens
        coin::merge(&mut pair.x_coin, x_amount_coin);
        coin::merge(&mut pair.y_coin, y_amount_coin);

        //calc liquidity
        let liquidity;
        let total_supply = *option::borrow(&coin::supply<LP<X,Y>>());

        if(total_supply == 0) {
            liquidity = sqrt(((x_amount * y_amount) as u128)) - MINIMUM_LIQUIDITY;
            let lp_locked = coin::mint(MINIMUM_LIQUIDITY,&pair.lp_mint);
            coin::merge(&mut pair.lp_locked, lp_locked);
        }else {
            liquidity = (min(Math::mul_div(x_amount,total_supply,x_reserve),Math::mul_div(y_amount,total_supply,y_reserve)) as u64);
        };

        // mint liquidity and return it
        let lp_coin = coin::mint<LP<X, Y>>(liquidity, &pair.lp_mint);
        let addr = address_of(sneder);
        if (!coin::is_account_registered<LP<X, Y>>(addr)) {
            coin::register<LP<X, Y>>(sneder);
        };
        coin::deposit(addr, lp_coin);
    }

    /*
       x_remove = liquidity * x_reserve / lp_total_supply
       y_remove = liquidity * y_reserve / lp_total_supply
    */
    public entry fun remove_liquidity<X,Y>(sneder:&signer,liquidity:u64) acquires Pair{
        //make sure lp exists
        assert!(exists<Pair<X,Y>>(@swap_account),1000);

        let pair = borrow_global_mut<Pair<X,Y>>(@swap_account);

        //burn liquidity
        let liquidity_coin = coin::withdraw<LP<X,Y>>(sneder,liquidity);
        coin::burn(liquidity_coin,&pair.lp_burn);

        let total_supply = *option::borrow(&coin::supply<LP<X,Y>>());
        let x_reserve = (coin::value(&pair.x_coin) as u128);
        let y_reserve = (coin::value(&pair.y_coin) as u128);

        let x_amount = Math::mul_div((liquidity as u128),x_reserve,total_supply);
        let y_amount = Math::mul_div((liquidity as u128),y_reserve,total_supply);

        let x_amount_coin = coin::extract<X>(&mut pair.x_coin,(x_amount as u64));
        let y_amount_coin = coin::extract<Y>(&mut pair.y_coin,(y_amount as u64));

        coin::deposit(address_of(sneder),x_amount_coin);
        coin::deposit(address_of(sneder),y_amount_coin);
    }

    /*
       amount_in = amount_in * 997 /1000
       amount_out = amount_in * out_reserve / (in_reserve + amount_in)
    */
    public entry fun swap_x_to_y<X,Y>(sender:&signer,amount_in:u64) acquires Pair{
        //make sure lp exists
        assert!(exists<Pair<X,Y>>(@swap_account),1000);

        let pair = borrow_global_mut<Pair<X,Y>>(@swap_account);

        let coin_in = coin::withdraw<X>(sender, amount_in);
        if (!coin::is_account_registered<Y>(address_of(sender))) {
            coin::register<Y>(sender);
        };

        let in_reserve = (coin::value(&pair.x_coin) as u128);
        let out_reserve = (coin::value(&pair.y_coin) as u128);

        let amount_out = get_amount_out((amount_in as u128),in_reserve,out_reserve);

        coin::merge(&mut pair.x_coin,coin_in);
        let amount_out_coin = coin::extract(&mut pair.y_coin,(amount_out as u64));

        coin::deposit(address_of(sender), amount_out_coin);
    }


    public fun pair_exist<X,Y>(addr:address):bool{
        exists<Pair<X,Y>>(addr) || exists<Pair<Y,X>>(addr)
    }

    public fun quote(x_amount:u128,x_reserve:u128,y_reserve:u128):u128{
        Math::mul_div(x_amount,y_reserve,x_reserve)
    }

    public fun get_amount_out(amount_in:u128,in_reserve:u128,out_reserve:u128):u128{
        let amount_in_with_fee = amount_in * 997;
        let numerator = amount_in_with_fee * out_reserve;
        let denominator = in_reserve * 1000 + amount_in_with_fee;
        numerator / denominator
    }

    public fun get_coin<X,Y>():(u64,u64) acquires Pair {
        let pair = borrow_global<Pair<X,Y>>(@swap_account);
        (coin::value(&pair.x_coin),coin::value(&pair.y_coin))
    }

}
```
