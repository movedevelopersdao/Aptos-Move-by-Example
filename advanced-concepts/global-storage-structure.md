# Global Storage Structure

In move the global storage structure is a persistent storage area that allows developers to store data on the blockchain.

The global storage structure in MOVE consists of a mapping data type, which is similar to a hash table or dictionary in other programming languages. This mapping data type allows developers to store key-value pairs, where the key is a unique identifier and the value is the data to be stored.

The purpose of Move programs is to `read from` and `write to` tree-shaped persistent global storage. Programs cannot access the filesystem, network, or any other data outside of this tree.

In pseudocode, the global storage looks something like:

```rust
struct GlobalStorage {
  resources: Map<(address, ResourceType), ResourceValue>
  modules: Map<(address, ModuleName), ModuleBytecode>
}
```

Structurally, global storage is a forest consisting of trees rooted at an account address. Each address can store both resource data values and module code values. As the pseudocode above indicates, each `address` can store at most one resource value of a given type and at most one module with a given name.
