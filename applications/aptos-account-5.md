# Lottery Contract

Sure! Here are some key points about a lottery contract that includes functions like initialize, place\_bet, getBalance, allPlayers, and pickWinner:

1. `Initialize`: The "initialize" function is used to set up the lottery contract. It initializes the necessary variables and establishes the contract owner.
2. `place_bet`: The "place\_bet" function allows players to participate in the lottery by placing their bets. Players can provide their desired bet amount as input to this function.
3. `getBalance`: The "getBalance" function retrieves the current balance of the lottery contract. It allows anyone to check the total amount of funds collected for the lottery.
4. `allPlayers`: The "allPlayers" function returns a list of all the players who have placed their bets in the lottery. It provides transparency and allows participants to verify their participation.
5. `pickWinner`: The "pickWinner" function is responsible for selecting the winner of the lottery. It employs a random selection algorithm or any predetermined criteria to choose the winner. Once the winner is determined, the contract owner can initiate the prize distribution process.

```rust
module my_addrx::Lottery { 

    use 0x1::signer;
    use 0x1::vector;
    use 0x1::coin;
    use 0x1::aptos_coin::AptosCoin; 
    use 0x1::aptos_account;
    use 0x1::aptos_coin;
    use std::timestamp; 
    use std::account;

    /// Error codes
    const STARTING_PRICE_IS_LESS: u64 = 0;
    const E_NOT_ENOUGH_COINS: u64 = 101;
    const PLAYERS_LESS_THAN_THREE: u64 = 2;
    const EINVALID_REWARD_AMOUNT: u64 = 3;
    const LESS_PRICE: u64 = 4;
    const EINSUFFICIENT_BALANCE: u64 = 5;

    struct Lotts has store,key{
        players: vector<address>,
        winner: address,
        totalamount: u64
    }

    public fun assert_is_owner(addr: address) {
        assert!(addr == @my_addrx, 0);
    }

    public fun assert_is_initialized(addr: address) {
        assert!(exists<Lotts>(addr), 1);
    }

    public fun assert_uninitialized(addr: address) {
        assert!(!exists<Lotts>(addr), 3);
    }   

    public fun initialize(acc: &signer){
        let addr = signer::address_of(acc);

        assert_is_owner(addr);
        assert_uninitialized(addr);

        let b_lot = Lotts{
            totalamount: 0,
            players: vector::empty<address>(),
            winner: @0x0,
            };
        move_to(acc, b_lot);
    }

    public entry fun place_bet(from: &signer, to_address: address, amount: u64) acquires Lotts{
        let from_acc_balance:u64 = coin::balance<AptosCoin>(signer::address_of(from));
        let addr = signer::address_of(from);

        assert!( amount <= from_acc_balance, E_NOT_ENOUGH_COINS);
        aptos_account::transfer(from,to_address,amount);

        let b_store = borrow_global_mut<Lotts>(to_address);
        vector::push_back(&mut b_store.players, addr);
        b_store.totalamount = b_store.totalamount + amount;
    }

    public fun getBalance(acc: &signer):u64 acquires Lotts{
        let addr = signer::address_of(acc);
        let b_store = borrow_global_mut<Lotts>(addr);

        assert_is_owner(addr);
        return b_store.totalamount
    }

    public fun allPlayers(store_addr: address):u64 acquires Lotts{
        let b_store = borrow_global_mut<Lotts>(store_addr);
        let total_players = vector::length(&b_store.players);
        return total_players
    }

    fun random():u64{
        let t=timestamp::now_microseconds();
        return t
    }

    public fun pickWinner(acc: &signer) acquires Lotts{
        let addr = signer::address_of(acc);
        let b_store = borrow_global_mut<Lotts>(addr);
        let total_players = vector::length(&b_store.players);

        assert_is_owner(addr);
        assert!(total_players>=3, PLAYERS_LESS_THAN_THREE);

        let r = random();
        let amount:u64;
        let _winner: address = @0x0; 
        let index = r % total_players;
        let better = *vector::borrow(&b_store.players, index);
        _winner = better;
        amount = b_store.totalamount;

        aptos_account::transfer(acc,_winner,amount); 
    }

    #[test(admin = @my_addrx,aptos_framework = @0x1)]
    fun test_flow(admin: signer , aptos_framework: &signer)acquires Lotts {

        timestamp::set_time_has_started_for_testing(aptos_framework);
        let owner = signer::address_of(&admin);
        let (burn_cap, mint_cap) = aptos_framework::aptos_coin::initialize_for_test(aptos_framework);
        let aptos_framework_address = signer::address_of(aptos_framework);
        account::create_account_for_test(aptos_framework_address);

        let bet = account::create_account_for_test(@0x3);
        let bet2 = account::create_account_for_test(@0x4);
        let bet3 = account::create_account_for_test(@0x5);
        let bet4 = account::create_account_for_test(@0x6);
        let bet5 = account::create_account_for_test(@0x7);// ATLEAST 3 BETTER
        

        coin::register<AptosCoin>(&bet);
        coin::register<AptosCoin>(&bet2);
        coin::register<AptosCoin>(&bet3);
        coin::register<AptosCoin>(&bet4);
        coin::register<AptosCoin>(&bet5);

        aptos_coin::mint(aptos_framework, @0x3, 30);
        aptos_coin::mint(aptos_framework, @0x4, 30);
        aptos_coin::mint(aptos_framework, @0x5, 30);
        aptos_coin::mint(aptos_framework, @0x6, 30);
        aptos_coin::mint(aptos_framework, @0x7, 30);

        initialize(&admin);

        place_bet(&bet,owner, 2);
        place_bet(&bet2,owner, 3);
        place_bet(&bet3,owner, 6);
        place_bet(&bet4,owner, 1);
        place_bet(&bet5,owner, 2);
        
        let totalPlayers = allPlayers(owner);
        let balance = getBalanace(&admin);

        assert!(totalPlayers == 5,0);
        assert!(balance == 14,0);

        pickWinner(&admin);// ATLEAST 3 BETTER REQUIRED

        coin::destroy_burn_cap(burn_cap);
        coin::destroy_mint_cap(mint_cap);
    }
}
```

These functions collectively form the core functionality of a lottery contract. They enable the initialization of the contract, allow players to place their bets, provide access to the contract balance, display the list of participants, and facilitate the selection of a winner.
