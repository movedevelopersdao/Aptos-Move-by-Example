# View functions

View function is a function that retrieves data from the blockchain without making any changes to it. It is used to read and display data stored on the blockchain.

View functions are important in the Aptos blockchain because they allow external applications to access and display data stored on the blockchain without the need for direct access to the blockchain itself. This can improve the efficiency and security of the blockchain network.

View functions are a recent addition to the Aptos blockchain that allows developers to create a GET API structure to display complex states of smart contracts. In the past, developers had to fetch all the module’s resource data and perform calculations on the client-side, which was time-consuming and inefficient. However, with view functions, developers can now retrieve complex states through a simpler and more streamlined process that saves time and resources. This development has significantly improved the usability of the Aptos blockchain and made it more accessible to developers.

```rust
#[view]
public fun get_todos(todo_address: address): vector<String> acquires TodoStore {
    borrow_global<TodoStore>(todo_address).todos
}
```

With the introduction of view functions, developers can now retrieve complex states from a smart contract in a more streamlined and efficient manner. View functions allow developers to define functions that can return specific data from a smart contract, providing a simple API that can be used by external invokers to retrieve data from the blockchain. This advancement has significantly improved the usability and accessibility of the Aptos blockchain, making it more developer-friendly and easier to use.

Instead of receiving the entire database every time you query it, you can now receive the specific data you need in the desired format using the new view functions.
