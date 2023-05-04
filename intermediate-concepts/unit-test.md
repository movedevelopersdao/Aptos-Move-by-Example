# Unit test

Unit testing for Move adds three new annotations to the Move source language:

* `#[test]`
* `#[test_only]`, and
* `#[expected_failure]`.

They respectively mark a function as a test, mark a module or module member (`use`, function, or struct) as code to be included for testing only, and mark that a test is expected to fail. These annotations can be placed on a function with any visibility. Whenever a module or module member is annotated as `#[test_only]` or `#[test]`, it will not be included in the compiled bytecode unless it is compiled for testing.

```rust
module my_addrx::Testing
{
    fun is_even(number : u64) : bool
    {
        if(number % 2==0)
        {
            true
        }
        else
        {
            false
        }
    }


    #[test]
    fun testing_is_even()
    {
        let x=is_even(14);
        assert!(x==true,1);

    }
}
```

Unit tests for a Move package can be run with the **`aptos move test`** command.

There are also a number of options that can be passed to the unit testing binary to fine-tune testing and to help debug failing tests. These can be found using the the help flag:

```rust
$ aptos move test -h
```

**Example:**

A simple module using some of the unit testing features is shown in the following example:

```rust
module my_addrx::my_module {

    struct MyCoin has key { value: u64 }

    public fun make_sure_non_zero_coin(coin: MyCoin): MyCoin {
        assert!(coin.value > 0, 0);
        coin
    }

    public fun has_coin(addr: address): bool {
        exists<MyCoin>(addr)
    }

    #[test]
    fun make_sure_non_zero_coin_passes() {
        let coin = MyCoin { value: 1 };
        let MyCoin { value: _ } = make_sure_non_zero_coin(coin);
    }

    #[test]
    // Or #[expected_failure] if we don't care about the abort code
    #[expected_failure(abort_code = 0, location = Self)]
    fun make_sure_zero_coin_fails() {
        let coin = MyCoin { value: 0 };
        let MyCoin { value: _ } = make_sure_non_zero_coin(coin);
    }

    #[test_only] // test only helper function
    fun publish_coin(account: &signer) {
        move_to(account, MyCoin { value: 1 })
    }

    #[test(a = @0x1, b = @0x2)]
    fun test_has_coin(a: signer, b: signer) {
        publish_coin(&a);
        publish_coin(&b);
        assert!(has_coin(@0x1), 0);
        assert!(has_coin(@0x2), 1);
        assert!(!has_coin(@0x3), 1);
    }
}
```

**Running test:**

```rust
INCLUDING DEPENDENCY AptosFramework
INCLUDING DEPENDENCY AptosStdlib
INCLUDING DEPENDENCY MoveStdlib
BUILDING aptos_by_examples
Running Move unit tests
[ PASS    ] 0x42::my_module::make_sure_non_zero_coin_passes
[ PASS    ] 0x42::my_module::make_sure_zero_coin_fails
[ PASS    ] 0x42::my_module::test_has_coin
Test result: OK. Total tests: 3; passed: 3; failed: 0
{
  "Result": "Success"
}
```
