---
description: Non-Fungible Token
---

# Aptos Token(Nft)

## Overview of NFT[​](https://aptos.dev/standards/aptos-token#overview-of-nft) <a href="#overview-of-nft" id="overview-of-nft"></a>

An [NFT](https://en.wikipedia.org/wiki/Non-fungible\_token) is a non-fungible [token](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/sources/token.move) or data stored on a blockchain that uniquely defines ownership of an asset. NFTs were first defined in [EIP-721](https://eips.ethereum.org/EIPS/eip-721) and later expanded upon in [EIP-1155](https://eips.ethereum.org/EIPS/eip-1155). NFTs are typically defined using the following properties:

* `name`: The name of the asset. It must be unique within a collection.
* `description`: The description of the asset.
* `uri`: A URL pointer to off-chain for more information about the asset. The asset could be media such as an image or video or more metadata.
* `supply`: The total number of units of this NFT. Many NFTs have only a single supply, while those that have more than one are referred to as editions.

Additionally, most NFTs are part of a collection or a set of NFTs with a common attribute, for example, a theme, creator, or minimally contract. Each collection has a similar set of attributes:

* `name`: The name of the collection. The name must be unique within the creator's account.
* `description`: The description of the collection.
* `uri`: A URL pointer to off-chain for more information about the asset. The asset could be media such as an image or video or more metadata.
* `supply`: The total number of NFTs in this collection.
* `maximum`: The maximum number of NFTs that this collection can have. If `maximum` is set to 0, then supply is untracked.

## Token resources[​](https://aptos.dev/standards/aptos-token#token-resources) <a href="#token-resources" id="token-resources"></a>

As shown in the diagram above, token-related data are stored at both the creator’s account and the owner’s account.

#### _Struct-level resources_[​](https://aptos.dev/standards/aptos-token#struct-level-resources) <a href="#struct-level-resources" id="struct-level-resources"></a>

The following tables describe fields at the struct level. For the definitive list, see the [Aptos Token Framework](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/overview.md).

_**Resource stored at the creator’s address**_[**​**](https://aptos.dev/standards/aptos-token#resource-stored-at-the-creators-address)

| Field                                                                                                                                                                   | Description                                                                                                                                                                                                                                                |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`Collections`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#resource-collections)                                  | Maintains a table called `collection_data`, which maps the collection name to the `CollectionData`. It also stores all the `TokenData` that this creator creates.                                                                                          |
| [`CollectionData`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#struct-collectiondata)                              | Stores the collection metadata. The supply is the number of tokens created for the current collection. The maxium is the upper bound of tokens in this collection.                                                                                         |
| [`CollectionMutabilityConfig`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#0x3\_token\_CollectionMutabilityConfig) | Specifies which field is mutable.                                                                                                                                                                                                                          |
| [`TokenData`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#0x3\_token\_TokenData)                                   | Acts as the main struct for holding the token metadata. Properties is a where users can add their own properties that are not defined in the token data. Users can mint more tokens based on the `TokenData`, and those tokens share the same `TokenData`. |
| [`TokenMutabilityConfig`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#0x3\_token\_TokenMutabilityConfig)           | Controls which fields are mutable.                                                                                                                                                                                                                         |
| [`TokenDataId`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#0x3\_token\_TokenDataId)                               | An ID used for representing and querying `TokenData` on-chain. This ID mainly contains three fields including creator address, collection name and token name.                                                                                             |
| [`Royalty`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#0x3\_token\_Royalty)                                       | Specifies the denominator and numerator for calculating the royalty fee. It also has the payee account address for depositing the royalty.                                                                                                                 |
| `PropertyValue`                                                                                                                                                         | Contains both value of a property and type of property.                                                                                                                                                                                                    |

_**Resource stored at the owner’s address**_[**​**](https://aptos.dev/standards/aptos-token#resource-stored-at-the-owners-address)

| Field                                                                                                                                   | Description                                                                                                                                                            |
| --------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`TokenStore`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#0x3\_token\_TokenStore) | The main struct for storing the token owned by this address. It maps `TokenId` to the actual token.                                                                    |
| [`Token`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#0x3\_token\_Token)           | The amount is the number of tokens.                                                                                                                                    |
| [`TokenId`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#0x3\_token\_TokenId)       | `TokenDataId` points to the metadata of this token. The `property_version` represents a token with mutated `PropertyMap` from `default_properties` in the `TokenData`. |

For more detailed descriptions, see [Aptos Token Framework](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/overview.md).

## Token creation <a href="#token-creation" id="token-creation"></a>

Every Aptos token belongs to a collection. The developer first needs to create a collection through `create_collection_script` and then create the token belonging to the collection `create_token_script`. To achieve parallel `TokenData` and `Token` creation, a developer can create unlimited collection and `TokenData` where the `maximum` of the collection and `TokenData` are set as 0. With this setting, the token contract won’t track the supply of types of token (`TokenData` count) and supply of token within each token type. As the result, the `TokenData` and token can be created in parallel.

```rust
/// create a empty token collection with parameters
public entry fun create_collection_script(
        creator: &signer,
        name: String,
        description: String,
        uri: String,
        maximum: u64,
        mutate_setting: vector<bool>,
    ) acquires Collections {
    
/// create token with raw inputs
 public entry fun create_token_script(
        account: &signer,
        collection: String,
        name: String,
        description: String,
        balance: u64,
        maximum: u64,
        uri: String,
        royalty_payee_address: address,
        royalty_points_denominator: u64,
        royalty_points_numerator: u64,
        mutate_setting: vector<bool>,
        property_keys: vector<String>,
        property_values: vector<vector<u8>>,
        property_types: vector<String>
    ) acquires Collections, TokenStore {    
```



Aptos also enforces simple validation of the input size and prevents duplication:

* Token name - unique within each collection
* Collection name - unique within each account
* Token and collection name length - smaller than 128 characters
* URI length - smaller than 512 characters
* Property map - can hold at most 1000 properties, and each key should be smaller than 128 characters

## Token burn <a href="#token-burn" id="token-burn"></a>

We provide `burn` and `burn_by_creator` functions for token owners and token creators to burn (or destroy) tokens. However, these two functions are also guarded by configs that are specified during the token creation so that both creator and owner are clear on who can burn the token. Burn is allowed only when the `BURNABLE_BY_OWNER` property is set to `true` in `default_properties`. Burn by creator is allowed when `BURNABLE_BY_CREATOR` is `true` in `default_properties`. Once all the tokens belonging to a `TokenData` are burned, the `TokenData` will be removed from the creator’s account. Similarly, if all `TokenData` belonging to a collection are removed, the `CollectionData` will be removed from the creator’s account.

```rust
/// The token is owned at address owner
    public entry fun burn_by_creator(
        creator: &signer,
        owner: address,
        collection: String,
        name: String,
        property_version: u64,
        amount: u64,
    ) acquires Collections, TokenStore {
    
/// Burn a token by the token owner
    public entry fun burn(
        owner: &signer,
        creators_address: address,
        collection: String,
        name: String,
        property_version: u64,
        amount: u64
    ) acquires Collections, TokenStore {
```

## Token transfer <a href="#token-transfer" id="token-transfer"></a>

We provide three different modes for transferring tokens between the sender and receiver.

_**Two-step transfer**_[**​**](https://aptos.dev/standards/aptos-token#two-step-transfer)

To protect users from receiving undesired NFTs, they must be first offered NFTs, and then accept the offered NFTs. Then only the offered NFTs will be deposited in the users' token stores. This is the default token transfer behavior. For example:

1. If Alice wants to send Bob an NFT, she must first offer Bob this NFT. This NFT is still stored under Alice’s account.
2. Only when Bob claims the NFT, will the NFT be removed from Alice’s account and stored in Bob’s token store.

#### _**Transfer with opt-in**_[**​**](https://aptos.dev/standards/aptos-token#transfer-with-opt-in)

If a user wants to receive direct transfer of the NFT, skipping the initial steps of offer and claim, then the user can call [`opt_in_direct_transfer`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#0x3\_token\_opt\_in\_direct\_transfer) to allow other people to directly transfer the NFTs into the user's token store. After opting into direct transfer, the user can call [`transfer`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#0x3\_token\_transfer) to transfer tokens directly. For example, Alice and anyone can directly send a token to Bob's token store once Bob opts in.

<pre class="language-rust"><code class="lang-rust">public entry fun opt_in_direct_transfer(
         account: &#x26;signer,
         opt_in: bool
         ) acquires TokenStore {

/// Transfers `amount` of tokens from `from` to `to`.
/// The receiver `to` has to opt-in direct transfer first
<strong>public entry fun transfer_with_opt_in(
</strong>        from: &#x26;signer,
        creator: address,
        collection_name: String,
        token_name: String,
        token_property_version: u64,
        to: address,
        amount: u64,
    ) acquires TokenStore {
</code></pre>

_**Multi-agent transfer**_[**​**](https://aptos.dev/standards/aptos-token#multi-agent-transfer)

The sender and receiver can both sign a transfer transaction to directly transfer a token from the sender to receiver [`direct_transfer_script`](https://github.com/aptos-labs/aptos-core/blob/main/aptos-move/framework/aptos-token/doc/token.md#function-direct\_transfer\_script). For example, once Alice and Bob both sign the transfer transaction, the token will be directly transferred from Alice's account to Bob.

```rust
public entry fun direct_transfer_script(
        sender: &signer,
        receiver: &signer,
        creators_address: address,
        collection: String,
        name: String,
        property_version: u64,
        amount: u64,
    ) acquires TokenStore {
```
