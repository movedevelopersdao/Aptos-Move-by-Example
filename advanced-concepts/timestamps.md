# Timestamps

**Module `0x1::timestamp`**

This module keeps a global wall clock that stores the current Unix time in microseconds.

**`now_microseconds`**

Gets the current time in microseconds.

**`now_seconds`**

Gets the current time in seconds.

```rust
module my_addrx::UnderstandingTimestamp{
  
    use std::timestamp; 
    
    public entry fun time()
    {
        let t1=timestamp::now_microseconds();
        std::debug::print(&t1);
        
        let t2=timestamp::now_seconds();
        std::debug::print(&t2);
    }
 
 
     #[test(framework = @0x1)]
    fun testing(framework: signer)
    {
        // set up global time for testing purpose
        timestamp::set_time_has_started_for_testing(&framework);   
        time(); 
    }   
}
```
