# Memory management

Memory management in Move and Solidity is quite different.

Move is  designed for developing smart contracts on the Libra blockchain. Move has a unique approach to memory management, where the ownership of data is transferred between resources in the program. Move uses a linear type system to enforce resource management, where a resource can only be consumed once. This means that once a resource is moved from one place to another, it can no longer be accessed by the original location. This helps prevent issues such as data races and use-after-free errors, which can be a problem in other programming languages.

Solidity is a programming language used to develop smart contracts on the Ethereum blockchain. Solidity uses a garbage collector to manage memory, which automatically frees memory that is no longer in use. Solidity has no concept of ownership or linear types, and instead relies on a heap-based memory management system similar to most general-purpose programming languages.

Overall, the memory management approach in Move is more strict and requires a different way of thinking about resource ownership and consumption compared to Solidity's garbage collector.
