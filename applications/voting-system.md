# Voting System

**Contract logic:**

* There are two resources `CandidateList` and `VoterList` and are stored under `my_addrx` (owner of this module).
* `making_candidate` function is called by the owner to add new candidate for election
* `giving_vote` function is called to vote for a given candidate
* `declare_winner` function is called to declare the result&#x20;

```rust
module my_addrx::Voting
{ 
    use std::vector;
    use std::simple_map::{SimpleMap,Self};
    use std::signer; 
    use aptos_framework::account;


    const E_CANDIDATE_ALREADY_EXISTS:u64=1;
    const E_ALREADY_VOTED:u64=2;
    const E_YOU_ARE_NOT_THE_OWNER:u64=3;
    const E_NO_ONE_VOTED:u64=4;

    struct CandidateList has key
    {
        candidate_list : SimpleMap< address,u64>,
        c_list : vector<address>,
        winner : address
    }


    struct VoterList has key
    {
        voters_list : SimpleMap<address,u8>
    }

    public entry fun making_candidiate(acc: &signer,c_addrx:address) acquires CandidateList
    {   
        let signer_address = signer::address_of(acc);
        assert!(signer_address==@my_addrx,E_YOU_ARE_NOT_THE_OWNER);

        if(!exists<CandidateList>(signer_address))
        { 
            let r = CandidateList{
                candidate_list:simple_map::create(),
                c_list:vector::empty<address>(),
                winner:@0x0
            };
            move_to(acc,r);

        };
        
        let map = borrow_global_mut<CandidateList>(@my_addrx);

        
        
        assert!(simple_map::contains_key(&mut map.candidate_list,&c_addrx)==false,E_CANDIDATE_ALREADY_EXISTS); 

        simple_map::add(&mut map.candidate_list,c_addrx ,0);
        vector::push_back(&mut map.c_list, c_addrx);

   
    }

    public entry fun giving_vote(acc: &signer,c_addrx:address,v_addrx:address) acquires VoterList ,CandidateList
    {
        let signer_address = signer::address_of(acc);
        assert!(signer_address==@my_addrx,E_YOU_ARE_NOT_THE_OWNER); 
        
        if(!exists<CandidateList>(signer_address))
        {
            let r = CandidateList{
                candidate_list:simple_map::create(),
                c_list:vector::empty<address>(),
                winner:@0x0
            };
            move_to(acc,r);

        };
        if(!exists<VoterList>(signer_address))
        {
            let r = VoterList{
                voters_list:simple_map::create()
            };
            move_to(acc,r);

        };

        let map = borrow_global_mut<CandidateList>(@my_addrx);
        let tmp= borrow_global_mut<VoterList>(@my_addrx);


        assert!(simple_map::contains_key(&mut tmp.voters_list,&v_addrx)==false,E_ALREADY_VOTED);
        
        simple_map::add(&mut tmp.voters_list,v_addrx ,1);

        if(simple_map::contains_key(&mut map.candidate_list,&c_addrx)){
            let v=simple_map::borrow_mut(&mut map.candidate_list,&c_addrx);
            *v=*v+1;
        }
        else
        {
            simple_map::add(&mut map.candidate_list,c_addrx ,1);
        }


    } 


    public entry fun declare_winner(acc: &signer)  acquires CandidateList
    {
        let signer_address = signer::address_of(acc);
        assert!(signer_address==@my_addrx,1); 


        if(!exists<CandidateList>(signer_address))
        {
            abort E_NO_ONE_VOTED 

        };



        let map = borrow_global<CandidateList>(@my_addrx);

        let winner:address=@0x0;
        let maxx:u64=0;
        let total_candidates = vector::length(&map.c_list);
        let i=0;
        while(i < total_candidates)
        {
            let can = *vector::borrow(& map.c_list,(i as u64));
            let votes=simple_map::borrow(& map.candidate_list,&can);
            
            if(maxx < *votes)
            {
                maxx = *votes;
                winner = can;
            };
            i=i+1;
        };


        let map = borrow_global_mut<CandidateList>(@my_addrx);
        map.winner = winner;



    }


    #[test(admin = @my_addrx)]
    public entry fun test_flow(admin: signer) acquires CandidateList,VoterList {

        account::create_account_for_test(signer::address_of(&admin));

        //adding candidates

        making_candidiate(&admin,@0x1);
        making_candidiate(&admin,@0x2);
        making_candidiate(&admin,@0x3);
        
        //This will result in error as the candidate already exists
        // making_candidiate(&admin,@0x3);




        //giving votes
        giving_vote(&admin,@0x1,@0x11);
        giving_vote(&admin,@0x3,@0x13);
        giving_vote(&admin,@0x2,@0x14);
        giving_vote(&admin,@0x3,@0x16);
        giving_vote(&admin,@0x1,@0x17);
        giving_vote(&admin,@0x3,@0x15);
        giving_vote(&admin,@0x2,@0x19);
        giving_vote(&admin,@0x2,@0x21);
        giving_vote(&admin,@0x2,@0x33);

        //This will result in error as same voter cannot vote again
        //giving_vote(&admin,@0x2,@0x33);



        //declaring winner

        declare_winner(&admin);

        let canList = borrow_global<CandidateList>(signer::address_of(&admin));

        assert!(canList.winner == @0x2,1);




    }




}
```
