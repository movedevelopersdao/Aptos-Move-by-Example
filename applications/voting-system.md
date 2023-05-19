# Voting System

**Contract logic:**

- There are two resources `CandidateList` and `VoterList` and are stored under `my_addrx` (owner of this module).
- `initialize_with_candidate` function is called by the owner to initialize and add a new candidate for election.
- `add_candidate` function is called by the owner to add more candidates.
- `vote` can be called only once by anybody
- `declare_winner` function is called to declare the result&#x20;

```rust
module my_addrx::Voting
{

use std::signer;
use std::vector;
use std::simple_map::{Self, SimpleMap};
use std::account;


const E_NOT_OWNER: u64 = 0;
const E_IS_NOT_INITIALIZED: u64 = 1;
const E_DOES_NOT_CONTAIN_KEY: u64 = 2;
const E_IS_INITIALIZED: u64 = 3;
const E_IS_INITIALIZED_WITH_CANDIDATE: u64 = 4;
const E_WINNER_DECLARED: u64 = 5;
const E_HAS_VOTED: u64 = 6;

struct CandidateList has key {
    candidate_list: SimpleMap<address, u64>,
    c_list: vector<address>,
    winner: address
}

struct VotingList has key {
    voters: SimpleMap<address, u64>
}

public fun assert_is_owner(addr: address) {
    assert!(addr == @my_addrx, 0);
}

public fun assert_is_initialized(addr: address) {
    assert!(exists<CandidateList>(addr), 1);
    assert!(exists<VotingList>(addr), 1);
}

public fun assert_uninitialized(addr: address) {
    assert!(!exists<CandidateList>(addr), 3);
    assert!(!exists<VotingList>(addr), 3);
}

public fun assert_contains_key(map: &SimpleMap<address, u64>, addr: &address) {
    assert!(simple_map::contains_key(map, addr), 2);
}

public fun assert_not_contains_key(map: &SimpleMap<address, u64>, addr: &address) {
    assert!(!simple_map::contains_key(map, addr), 4);
}



public entry fun initialize_with_candidate(acc: &signer, c_addr: address) acquires CandidateList {

    let addr = signer::address_of(acc);

    assert_is_owner(addr);
    assert_uninitialized(addr);

    let c_store = CandidateList{
        candidate_list:simple_map::create(),
        c_list: vector::empty<address>(),

        winner: @0x0,
        };

        move_to(acc, c_store);

    let v_store = VotingList {
        voters:simple_map::create(),
        };

        move_to(acc, v_store);

    let c_store = borrow_global_mut<CandidateList>(addr);
    simple_map::add(&mut c_store.candidate_list, c_addr, 0);
    vector::push_back(&mut c_store.c_list, c_addr);
}

public entry fun add_candidate(acc: &signer, c_addr: address) acquires CandidateList {
    let addr = signer::address_of(acc);
    assert_is_owner(addr);
    assert_is_initialized(addr);

    let c_store = borrow_global_mut<CandidateList>(addr);
    assert!(c_store.winner == @0x0, 5);
    assert_not_contains_key(&c_store.candidate_list, &c_addr);
    simple_map::add(&mut c_store.candidate_list, c_addr, 0);
    vector::push_back(&mut c_store.c_list, c_addr);
}

public entry fun vote(acc: &signer, c_addr: address, store_addr: address) acquires CandidateList, VotingList{
    let addr = signer::address_of(acc);

    assert_is_initialized(store_addr);

    let c_store = borrow_global_mut<CandidateList>(store_addr);
    let v_store = borrow_global_mut<VotingList>(store_addr);
    assert!(c_store.winner == @0x0, 5);
    assert!(!simple_map::contains_key(&v_store.voters, &addr), 6);
    assert_contains_key(&c_store.candidate_list, &c_addr);
    let votes = simple_map::borrow_mut(&mut c_store.candidate_list, &c_addr);
    *votes = *votes + 1;
    simple_map::add(&mut v_store.voters, addr, 1);
}

public entry fun declare_winner(acc: &signer) acquires CandidateList {
    let addr = signer::address_of(acc);
    assert_is_owner(addr);
    assert_is_initialized(addr);

    let c_store = borrow_global_mut<CandidateList>(addr);
    assert!(c_store.winner == @0x0, 5);

    let candidates = vector::length(&c_store.c_list);

    let i = 0;
    let winner: address = @0x0;
    let max_votes: u64 = 0;

    while (i < candidates) {
        let candidate = *vector::borrow(&c_store.c_list, (i as u64));
        let votes = simple_map::borrow(&c_store.candidate_list, &candidate);

        if(max_votes < *votes) {
            max_votes = *votes;
            winner = candidate;
        };
        i = i + 1;
    };

    c_store.winner = winner;
}

#[test(admin = @my_addrx)]

public entry fun test_flow(admin: signer) acquires CandidateList, VotingList {
    let c_addr = @0x1;
    let c_addr2 = @0x2;
    let voter = account::create_account_for_test(@0x3);
    let voter2 = account::create_account_for_test(@0x4);
    let voter3 = account::create_account_for_test(@0x5);
    initialize_with_candidate(&admin, c_addr);
    add_candidate(&admin, c_addr2);
    let candidate_list = &borrow_global<CandidateList>(signer::address_of(&admin)).candidate_list;
    assert_contains_key(candidate_list, &c_addr);
    assert_contains_key(candidate_list, &c_addr2);


    vote(&voter, c_addr, signer::address_of(&admin));
    vote(&voter2, c_addr, signer::address_of(&admin));
    vote(&voter3, c_addr2, signer::address_of(&admin));

    let voters = &borrow_global<VotingList>(signer::address_of(&admin)).voters;
    assert_contains_key(voters, &signer::address_of(&voter));
    assert_contains_key(voters, &signer::address_of(&voter2));
    assert_contains_key(voters, &signer::address_of(&voter3));

    declare_winner(&admin);
    let winner = &borrow_global<CandidateList>(signer::address_of(&admin)).winner;
    assert!(winner == &c_addr, 0);
}

#[test(admin = @my_addrx)]
#[expected_failure(abort_code = E_WINNER_DECLARED)]
public entry fun test_declare_winner(admin: signer) acquires CandidateList, VotingList {
    let c_addr = @0x1;
    let c_addr2 = @0x2;
    let voter = account::create_account_for_test(@0x3);
    let voter2 = account::create_account_for_test(@0x4);
    let voter3 = account::create_account_for_test(@0x5);
    initialize_with_candidate(&admin, c_addr);
    add_candidate(&admin, c_addr2);

    vote(&voter, c_addr, signer::address_of(&admin));
    vote(&voter2, c_addr, signer::address_of(&admin));
    vote(&voter3, c_addr2, signer::address_of(&admin));

    declare_winner(&admin);
    declare_winner(&admin);
}

#[test]
#[expected_failure(abort_code = E_NOT_OWNER)]

public entry fun test_initialize_with_candidate_not_owner() acquires CandidateList {
    let c_addr = @0x1;
    let not_owner = account::create_account_for_test(@0x2);
    initialize_with_candidate(&not_owner, c_addr);
}

#[test(admin = @my_addrx)]
#[expected_failure(abort_code = E_IS_INITIALIZED)]
public entry fun test_initialize_with_same_candidate(admin: signer) acquires CandidateList {
    let c_addr = @0x1;
    initialize_with_candidate(&admin, c_addr);
    initialize_with_candidate(&admin, c_addr);
}

#[test(admin = @my_addrx)]
#[expected_failure(abort_code = E_HAS_VOTED)]

public entry fun test_vote_twice(admin: signer) acquires CandidateList, VotingList {
    let c_addr = @0x1;
    let voter = account::create_account_for_test(@0x2);
    initialize_with_candidate(&admin, c_addr);
    vote(&voter, c_addr, signer::address_of(&admin));
    vote(&voter, c_addr, signer::address_of(&admin));
}

#[test(admin = @my_addrx)]
#[expected_failure(abort_code = E_IS_NOT_INITIALIZED)]

public entry fun test_vote_not_initialized(admin: signer) acquires CandidateList, VotingList {
    let c_addr = @0x1;
    let voter = account::create_account_for_test(@0x2);
    vote(&voter, c_addr, signer::address_of(&admin));
}

#[test(admin = @my_addrx)]
#[expected_failure(abort_code = E_WINNER_DECLARED)]

public entry fun test_add_candidate_after_winner_declared(admin: signer) acquires CandidateList, VotingList {
    let c_addr = @0x1;
    let c_addr2 = @0x2;
    let c_addr3 = @0x3;
    let voter = account::create_account_for_test(@0x2);
    let voter2 = account::create_account_for_test(@0x3);
    initialize_with_candidate(&admin, c_addr);
    add_candidate(&admin, c_addr2);
    vote(&voter, c_addr, signer::address_of(&admin));
    vote(&voter2, c_addr, signer::address_of(&admin));
    declare_winner(&admin);
    add_candidate(&admin, c_addr3);
}

}
```