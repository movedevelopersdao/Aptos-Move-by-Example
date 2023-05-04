---
description: >-
  Properties of Move Language that makes it a more secure language for writing
  smart contracts.
---

# Why is Move Secure

Securing smart contracts is vital in the blockchain sector, as even a small vulnerability can result in disastrous consequences due to the significant value of the assets at stake. As such, selecting an appropriate programming language is essential in ensuring the reliability and security of smart contracts.

The primary goal of safety in smart contract languages is to understand the contract's inner workings and prevent potential problems arising from bugs or security flaws, such as the reentrancy issue, a common problem in smart contracts where multiple self-calls are executed, causing a drain of funds.

The Move language equips developers with valuable tools like the **Move prover**, which aids in verifying the correct operation of their contracts.

Move Prover is formal verification tool, formal verification is the process of using mathematical and logical methods to prove that a program meets its specification.

Other security features of Move :&#x20;

* **Linear Type System**: Move features a robust linear type system that guarantees specific access and modification patterns for data structures within a Move program. This aids in avoiding unforeseen behaviours.
* **Visibility System**: Move incorporates a solid visibility system, enabling developers to precisely manage data access, modification, and storage within a blockchain. This assists in preventing unauthorised access to sensitive information or code.
* **Modularity**: Move is highly modular, enabling resource ownership by modules, which means that only the module responsible for defining a resource can modify it. This establishes a secure data structure, as only authorized modules can access and modify the information within.

For more details check here : [https://pontem.network/posts/what-makes-move-safe](https://pontem.network/posts/what-makes-move-safe)
