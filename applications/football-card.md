# Football Card

**Contract logic:**



* Create a  `newStar` function first and then `mint` to your address.
* Owner can call `get`, `setPrice` and `transfer` to other address too.
* You can call `card_exists` to check your card is actually exist to your address or not.
* Here the some `test_football` test for better understandng.

<pre class="language-rust"><code class="lang-rust"><strong>  module card_addrx::Football_card{
</strong>        use std::signer;
        use std::debug;
        //error code
        const STAR_AlREADY_EXISTS:u64 = 100;
        const STAR_NOT_EXISTS:u64 = 101;
        //struct info
        struct FootBallStar has key,drop{
            name: vector&#x3C;u8>,
            country: vector&#x3C;u8>,
            postion: u8,
            value: u64,
        }

    public fun newStar(
            name: vector&#x3C;u8>,
            country: vector&#x3C;u8>,
            postion: u8
        ):FootBallStar{
            FootBallStar{
                name,
                country,
                postion,
                value:0
            }
    }

    public fun mint(to: &#x26;signer,star: FootBallStar){
            let acc_addr = signer::address_of(to);
            assert!(!card_exists(acc_addr),STAR_AlREADY_EXISTS);
            move_to&#x3C;FootBallStar>(to,star)
    }

    public fun get(owner: &#x26;signer):(vector&#x3C;u8>,u64) acquires FootBallStar{
            let acc_addr = signer::address_of(owner);
            let star = borrow_global_mut&#x3C;FootBallStar>(acc_addr);
            (star.name,star.value)
    }

    public fun card_exists(acc: address):bool{
            exists&#x3C;FootBallStar>(acc)
    }

    public fun setPrice(owner: &#x26;signer,price: u64) acquires FootBallStar{
            let acc_addr = signer::address_of(owner);
            assert!(card_exists(acc_addr),STAR_NOT_EXISTS);
            let star = borrow_global_mut&#x3C;FootBallStar>(acc_addr);
            star.value = price
    }

    public fun transfer(owner: &#x26;signer,to: &#x26;signer) acquires FootBallStar{
            let acc_addr = signer::address_of(owner);
            assert!(card_exists(acc_addr),STAR_NOT_EXISTS);
            let star = move_from&#x3C;FootBallStar>(acc_addr);
            let acc_addr2 = signer::address_of(to);
            move_to&#x3C;FootBallStar>(to,star);
            assert!(card_exists(acc_addr2),100);
        }

        #[test(owner=@0x123,to=@0x768)]
        fun test_football(owner: signer,to: signer)acquires FootBallStar{

            //FOOTBALL_CARD
            let star = newStar(b"Sunil Chhetri",b"India",2);
            mint(&#x26;owner,star);
            let (name,value) = get(&#x26;owner);
            debug::print(&#x26;name);
            debug::print(&#x26;value);
            setPrice(&#x26;owner,100);
            transfer(&#x26;owner,&#x26;to); 
            let (name,value) = get(&#x26;to);
            debug::print(&#x26;name);
            debug::print(&#x26;value);
        }
<strong>}
</strong></code></pre>
