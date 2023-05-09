# Move prover

A Move prover is a tool that verifies the correctness of smart contracts written in the Move programming language. The Move prover helps ensure that these smart contracts behave correctly by mathematically proving the correctness of the code.

The Move prover works by automatically verifying that the smart contract code adheres to certain properties, such as not allowing for integer overflow or underflow. It also checks that the code is free from certain classes of bugs, such as reentrancy vulnerabilities and division by zero errors. If the Move prover finds any violations, it reports them back to the developer, allowing them to fix the code before it is deployed.

The Move Prover exists to make contracts more _trustworthy_; it:

* Protects massive assets managed by the Aptos blockchain from smart contract bugs
* Protects against well-resourced adversaries
* Anticipates justified regulator scrutiny and compliance requirements
* Allows domain experts with a mathematical background, but not necessarily a software engineering background, to understand what smart contracts do

{% hint style="info" %}
Currently, Windows is not supported by the Move Prover.
{% endhint %}

```rust
module my_addrx::Example{
    struct Counter has key {
        value: u8,
    }

    public fun increment(a: address) acquires Counter {
        let c = borrow_global_mut<Counter>(a);
        c.value = c.value + 1;
    }

    spec increment {
        aborts_if !exists<Counter>(a);
        ensures global<Counter>(a).value == old(global<Counter>(a)).value + 1;
    }
}
```

The Move Prover is invoked via the Aptos cli.

Call the Move Prover from the directory to be tested or by supplying its path to the `--package-dir` argument:

*   Prove the sources of the package in the current directory:

    ```rust
    aptos move prove
    ```
*   Prove the sources of the package at \<path>:

    ```rust
    aptos move prove --package-dir <path>
    ```

Now if we run `aptos move prove` command for our code it will lead to following error.

```bash
error: abort not covered by any of the `aborts_if` clauses
   │  
 8 │           c.value = c.value + 1;
   │                             - abort happened here with execution failure
   ·  
11 │ ╭     spec increment {
12 │ │         aborts_if !exists<Counter>(a);
13 │ │         ensures global<Counter>(a).value == old(global<Counter>(a)).value + 1;
14 │ │     }
   │ ╰─────^
{
  "Error": "Move Prover failed: exiting with verification errors"
}
```

The Move Prover has generated an example counter that leads to an overflow when adding 1 to the value of 255 for an `u8`. This overflow occurs if the function specification calls for abort behavior, but the condition under which the function is aborting is not covered by the specification. And in fact, with `aborts_if !exists<Counter>(a)`, we only cover the abort caused by the absence of the resource, but not the abort caused by the arithmetic overflow.

Let's fix the above code and add the following condition:

```rust
spec increment {
    aborts_if global<Counter>(a).value == 255;
    ...
}
```

With this, the Move Prover will succeed without any errors.
