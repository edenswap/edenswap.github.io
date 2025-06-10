---
title: Router
parent: Contract guides
nav_order: 1
---

## Read-Only Functions
```
public fun optimal_liquidity_amounts(
    tokenA: Object<Metadata>,
    tokenB: Object<Metadata>,
    amountADesired: u128,
    amountBDesired: u128,
): (u128, u128)
```

Returns the optimal amounts of tokens to provide as liquidity given the desired amount of each token to add. The returned values are the amounts of tokenA, tokenB.

| Name           | Type      |                                                              |
| :------------- | :-------- | :----------------------------------------------------------- |
| tokenA         | `address` | A pool token.                                                |
| tokenB         | `address` | A pool token.                                                |
| amountADesired | `u128`    | The amount of tokenA to add as liquidity if the B/A price is <= amountBDesired/amountADesired (A depreciates). |
| amountBDesired | `u128`    | The amount of tokenB to add as liquidity if the A/B price is <= amountADesired/amountBDesired (B depreciates). |

```
public fun optimal_liquidity_amount_opposite(
    pool: address,
    tokenA: Object<Metadata>,
    amount: u128,
): u128
```

When calculating liquidity, given the `amount` of `tokenA` and the address of the `pool`, calculate the amount of tokenB.

## State-Changing Functions

### create_pool

```
public entry fun create_pool(
    creator: &signer,
    tokenA: Object<Metadata>,
    tokenB: Object<Metadata>,
    amountA: u128,
    amountB: u128,
)
```

Create a pool with tokens pair and add initial liquidity, **one of tokenA/tokenB must be EDS** at current.	

| Name    | Type      |                                           |
| :------ | :-------- | :---------------------------------------- |
| tokenA  | `address` | A token address.                          |
| tokenB  | `address` | A token address.                          |
| amountA | `u128`    | The amount of tokenA to add as liquidity. |
| amountB | `u128`    | The amount of tokenB to add as liquidity. |

### add_pair_liquidity_entry

```
public entry fun add_pair_liquidity_entry(
    provider: &signer,
    pool: address,
    amount_0_desired: u128,
    amount_1_desired: u128,
)
```

Add liquidity to a `pool`. The user specifies the desired amount of each token to add and this will add the optimal amounts. If no optimal amounts can be found, this will fail.

### add_single_liquidity_entry

```
public entry fun add_single_liquidity_entry(
    provider: &signer,
    pool: address,
    token: Object<Metadata>,
    amount_desired: u128,
)
```

Similar to `add_pair_liquidity_entry`, but only add single token with other side pairing with virtual token.

### remove_liquidity_entry

```
public entry fun remove_liquidity_entry(
    provider: &signer,
    pool: address,
    liquidity: u128,
)
```

Remove an amount of `liquidity` from a `pool`.

### swap_exact_in_entry

```
public entry fun swap_exact_in_entry(
    user: &signer,
    token_in: Object<Metadata>,
    token_out: Object<Metadata>,
    amount_in: u128,
    amount_out_min: u128,
)
```

Swap exact `amount_in` of token `token_in` to `token_out` , **one of token_in/token_out must be EDS** at current. User can specifies the minimum amount of `token_out` they expect to receive. If the actual amount received is less than `amount_out_min`, the transaction will fail.

### swap_exact_in_2pools

```
public entry fun swap_exact_in_2pools(
    sender: &signer,
    pools: vector<address>,
    token_in: Object<Metadata>,
    amount_in: u128,
    amount_out_min: u128,
)
```

Similar to `swap_exact_in_entry`, implement by swapping token_in->EDS in pools[0] and EDS -> token_out in pools[1], .

### swap_exact_out_entry

```
public entry fun swap_exact_out_entry(
    user: &signer,
    token_in: Object<Metadata>,
    token_out: Object<Metadata>,
    amount_out: u128,
    amount_in_max: u128,
)
```

Swap exact `amount_out` of token `token_out` from `token_in` , **one of token_in/token_out must be EDS** at current. User can specifies the maximum amount of `token_in` they want to send. If the actual amount received is more than `amount_in_max`, the transaction will fail.

### swap_exact_out_2pools

```
public entry fun swap_exact_out_2pools(
    sender: &signer,
    pools: vector<address>,
    token_out: Object<Metadata>,
    amount_out: u128,
    amount_in_max: u128,
)
```

Similar to `swap_exact_out_entry`, implement by swapping token_in->EDS in pools[0] and EDS -> token_out in pools[1].

