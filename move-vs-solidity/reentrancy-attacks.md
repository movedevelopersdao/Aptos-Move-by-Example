# Reentrancy attacks

Reentrancy attacks are a type of vulnerability that can occur in smart contracts, where a malicious user can exploit a flaw in the contract code to repeatedly execute a function before the previous function execution has completed. This can lead to unexpected behavior and can allow the attacker to steal funds or manipulate the contract state.

Both Solidity and Move have mechanisms in place to prevent reentrancy attacks, but they have different approaches.

In `Solidity`, the recommended approach to prevent reentrancy attacks is to use the "checks-effects-interactions" pattern, which involves performing checks on user inputs and contract state before executing any external interactions. This pattern ensures that the contract state is updated before any external interactions occur, which prevents any potential reentrancy attacks.

In `Move`, reentrancy attacks are prevented by the resource model, which ensures that a resource can only be accessed by a single execution context at a time. This means that if a function execution is not complete, other functions cannot access the same resource until the execution is complete. This prevents reentrancy attacks because a malicious user cannot repeatedly execute a function before the previous execution has completed.

Overall, both Solidity and Move have mechanisms in place to prevent reentrancy attacks. However, Solidity relies on a programming pattern to prevent these attacks, while Move uses the resource model to prevent them. The choice between the two ultimately depends on the specific requirements of the application and the platform being used.
