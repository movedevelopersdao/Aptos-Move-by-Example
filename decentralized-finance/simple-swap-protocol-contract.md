# Simple Swap Protocol Contract

## Overview

The Simple Swap Protocol contract implements a basic swap protocol for generating LP (Liquidity Provider) tokens, creating liquidity pools, adding/removing liquidity, and swapping tokens. It allows users to participate in liquidity provision and token swapping in a decentralized manner.

### Structs:

1. `LP<phantom X, phantom Y>`
   * A generic struct representing LP tokens for a specific token pair X-Y in the contract.
   * LP tokens represent ownership in liquidity pools.
2. `Pair<phantom X, phantom Y>`
   * A generic struct representing a liquidity pool for a specific token pair X-Y in the contract.
   * Contains fields to store the reserves of token X and Y, locked LP tokens, and mint/burn capabilities for LP tokens.

### Functions:

**`generate_lp_name_symbol<X, Y>(): String`**

* Generates a name symbol for LP tokens corresponding to the token pair X-Y.
* Concatenates "LP-", the symbol of token X, "-", and the symbol of token Y to form the LP token name symbol.

**`create_pool<X, Y>(sender: &signer)`**

* Creates a new liquidity pool for the token pair X-Y.
* Ensures no duplicate pools are created for the same token pair.
* Initializes the LP token for the pool to track liquidity provided by users.

**`add_liquidity<X, Y>(sender: &signer, x_amount: u64, y_amount: u64) acquires Pair`**

* Allows users to add liquidity to the pool by providing amounts of token X and Y.
* Calculates the optimal amount of Y tokens to be added given the amount of X tokens (or vice versa) to maintain a balanced pool.
* Mints LP tokens corresponding to the provided liquidity and deposits them into the user's account.

**`remove_liquidity<X, Y>(sender: &signer, liquidity: u64) acquires Pair`**

* Allows users to remove liquidity from the pool by burning their LP tokens.
* Calculates the amounts of token X and Y that the user will receive based on the proportion of their LP tokens to the total supply.
* Deposits the corresponding amounts of X and Y tokens into the user's account.

**`swap_x_to_y<X, Y>(sender: &signer, amount_in: u64) acquires Pair`**

* Allows users to swap token X for token Y.
* Calculates the amount of token Y that the user will receive in exchange for the provided amount of token X, considering a 0.3% fee.
* Deposits the received token Y into the user's account.

### Helper Functions:

**`pair_exist<X, Y>(addr: address) -> bool`**

* Checks if a liquidity pool exists for the token pair X-Y or Y-X.
* Returns true if a pool exists, otherwise false.

**`quote(x_amount: u128, x_reserve: u128, y_reserve: u128) -> u128`**

* Calculates the optimal amount of token Y given the amount of token X, X reserve, and Y reserve.

**`get_amount_out(amount_in: u128, in_reserve: u128, out_reserve: u128) -> u128`**

* Calculates the amount of output token for a given amount of input token in a swap, considering a 0.3% fee.

**`get_coin<X, Y>() -> (u64, u64) acquires Pair`**

* Returns the current reserves of token X and Y in the liquidity pool.

This structured heading information should help your viewers better understand the contract and its functionalities in your Gitbook. Feel free to use and modify it as needed!
