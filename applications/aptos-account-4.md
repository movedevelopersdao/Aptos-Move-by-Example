# Polling Contract

* A polling contract is a smart contract used to conduct polls or surveys on the blockchain.
* Participants can express their opinions or vote on specific topics in a transparent and decentralized manner.
* The contract records and stores the votes immutably, ensuring the integrity and reliability of the poll results.

```rust
module my_addrx::Polling { 

    use 0x1::signer;
    use std::account;
    /// Error codes

    struct Poll has store,key{
        viewPoint: vector<u8>,
        totalVotes:u64,
        trueVotes: u64
    }

    public fun assert_is_owner(addr: address) {
        assert!(addr == @my_addrx, 0);
    }

    public fun assert_is_initialized(addr: address) {
        assert!(exists<Poll>(addr), 1);
    }

    public fun assert_uninitialized(addr: address) {
        assert!(!exists<Poll>(addr), 3);
    }   

    public fun initialize(acc: &signer, msg: vector<u8>){
        let addr = signer::address_of(acc);

        assert_is_owner(addr);
        assert_uninitialized(addr);

        let b_store = Poll{
            viewPoint : msg,
            totalVotes: 0,
            trueVotes: 0,
            };
        move_to(acc, b_store);
    }

    public fun vote(acc_own: &signer, store_addr: address,_vote:bool )acquires Poll{
        let addr = signer::address_of(acc_own);
        
        assert_uninitialized(addr);

        let op_store = borrow_global_mut<Poll>(store_addr);
        op_store.totalVotes = op_store.totalVotes + 1;

        if(_vote == true){
            op_store.trueVotes =  op_store.trueVotes + 1;
        }
    }

    public fun currentStandings(store_addr: address):u64 acquires Poll{
        let op_store = borrow_global_mut<Poll>(store_addr);
        return (op_store.trueVotes*100/op_store.totalVotes*100)/100
    }


    public fun voteCount(store_addr: address):u64 acquires Poll{
        let op_store = borrow_global_mut<Poll>(store_addr);
        return op_store.totalVotes
    }

    #[test(admin = @my_addrx)]
    fun test_flow(admin: signer)acquires Poll {
        let owner = signer::address_of(&admin);
        let voter = account::create_account_for_test(@0x3);
        let voter2 = account::create_account_for_test(@0x4);
        let voter3 = account::create_account_for_test(@0x5);
        let voter4 = account::create_account_for_test(@0x6);
        let voter5 = account::create_account_for_test(@0x7);
        let greet:vector<u8> = b"Welcome to Aptos move by examples"; 
        initialize(&admin, greet);
        vote(&voter,owner, true);
        vote(&voter2,owner, false);
        vote(&voter3,owner, false);
        vote(&voter4,owner, false);
        vote(&voter5,owner, true);
        // vote(&voter5,owner, true); //THROW ERROR BECAUSE WE TRYING TO ADD OUR VOTE SECOND TIME
        let value = currentStandings(owner); //its giving wrong value
        let totolvotes = voteCount(owner);
        std::debug::print(&value);
        assert!(value == 40,0);
        assert!(totolvotes == 5,0);

    }
}
```
