---
title: Router
parent: Contract guides
nav_order: 1
---

## Overview
The Router contract serves as the main interface for interacting with EdenSwap's liquidity pools. It provides functions for creating pools, managing liquidity, and executing swaps.

## Read-Only Functions

### optimal_liquidity_amounts
```rust
public fun optimal_liquidity_amounts(
    tokenA: Object<Metadata>,
    tokenB: Object<Metadata>,
    amountADesired: u128,
    amountBDesired: u128,
): (u128, u128)
```
Calculates the optimal amounts of tokens to provide as liquidity based on desired amounts.

| Parameter | Type | Description |
|-----------|------|-------------|
| tokenA | `Object<Metadata>` | First token in the pair |
| tokenB | `Object<Metadata>` | Second token in the pair |
| amountADesired | `u128` | Desired amount of tokenA to add |
| amountBDesired | `u128` | Desired amount of tokenB to add |

**Returns**: Tuple of (optimal amount of tokenA, optimal amount of tokenB)

### optimal_liquidity_amount_opposite
```rust
public fun optimal_liquidity_amount_opposite(
    pool: address,
    tokenA: Object<Metadata>,
    amount: u128,
): u128
```
Calculates the required amount of the opposite token when adding single-sided liquidity.

| Parameter | Type | Description |
|-----------|------|-------------|
| pool | `address` | Address of the liquidity pool |
| tokenA | `Object<Metadata>` | Token being added |
| amount | `u128` | Amount of tokenA to add |

**Returns**: Required amount of the opposite token

## State-Changing Functions

### create_pool
```rust
public entry fun create_pool(
    creator: &signer,
    tokenA: Object<Metadata>,
    tokenB: Object<Metadata>,
    amountA: u128,
    amountB: u128,
)
```
Creates a new liquidity pool and adds initial liquidity. **Note**: One of the tokens must be EDS.

| Parameter | Type | Description |
|-----------|------|-------------|
| creator | `&signer` | Signer reference of the creator |
| tokenA | `Object<Metadata>` | First token in the pair |
| tokenB | `Object<Metadata>` | Second token in the pair |
| amountA | `u128` | Initial amount of tokenA |
| amountB | `u128` | Initial amount of tokenB |

### add_pair_liquidity_entry
```rust
public entry fun add_pair_liquidity_entry(
    provider: &signer,
    pool: address,
    amount_0_desired: u128,
    amount_1_desired: u128,
)
```
Adds liquidity to a pool with optimal amounts of both tokens.

| Parameter | Type | Description |
|-----------|------|-------------|
| provider | `&signer` | Signer reference of the liquidity provider |
| pool | `address` | Address of the liquidity pool |
| amount_0_desired | `u128` | Desired amount of first token |
| amount_1_desired | `u128` | Desired amount of second token |

### add_single_liquidity_entry
```rust
public entry fun add_single_liquidity_entry(
    provider: &signer,
    pool: address,
    token: Object<Metadata>,
    amount_desired: u128,
)
```
Adds single-sided liquidity to a pool, with the opposite side paired with virtual tokens.

| Parameter | Type | Description |
|-----------|------|-------------|
| provider | `&signer` | Signer reference of the liquidity provider |
| pool | `address` | Address of the liquidity pool |
| token | `Object<Metadata>` | Token to add as liquidity |
| amount_desired | `u128` | Desired amount of token to add |

### remove_liquidity_entry
```rust
public entry fun remove_liquidity_entry(
    provider: &signer,
    pool: address,
    liquidity: u128,
)
```
Removes liquidity from a pool.

| Parameter | Type | Description |
|-----------|------|-------------|
| provider | `&signer` | Signer reference of the liquidity provider |
| pool | `address` | Address of the liquidity pool |
| liquidity | `u128` | Amount of LP tokens to burn |

### swap_exact_in_entry
```rust
public entry fun swap_exact_in_entry(
    user: &signer,
    token_in: Object<Metadata>,
    token_out: Object<Metadata>,
    amount_in: u128,
    amount_out_min: u128,
)
```
Swaps an exact amount of input token for output token. **Note**: One of the tokens must be EDS.

| Parameter | Type | Description |
|-----------|------|-------------|
| user | `&signer` | Signer reference of the user |
| token_in | `Object<Metadata>` | Input token |
| token_out | `Object<Metadata>` | Output token |
| amount_in | `u128` | Exact amount of input token |
| amount_out_min | `u128` | Minimum amount of output token to receive |

### swap_exact_in_2pools
```rust
public entry fun swap_exact_in_2pools(
    sender: &signer,
    pools: vector<address>,
    token_in: Object<Metadata>,
    amount_in: u128,
    amount_out_min: u128,
)
```
Executes a swap through two pools, using EDS as an intermediate token.

| Parameter | Type | Description |
|-----------|------|-------------|
| sender | `&signer` | Signer reference of the sender |
| pools | `vector<address>` | Vector of two pool addresses |
| token_in | `Object<Metadata>` | Input token |
| amount_in | `u128` | Amount of input token |
| amount_out_min | `u128` | Minimum amount of output token to receive |

### swap_exact_out_entry
```rust
public entry fun swap_exact_out_entry(
    user: &signer,
    token_in: Object<Metadata>,
    token_out: Object<Metadata>,
    amount_out: u128,
    amount_in_max: u128,
)
```
Swaps input token for an exact amount of output token. **Note**: One of the tokens must be EDS.

| Parameter | Type | Description |
|-----------|------|-------------|
| user | `&signer` | Signer reference of the user |
| token_in | `Object<Metadata>` | Input token |
| token_out | `Object<Metadata>` | Output token |
| amount_out | `u128` | Exact amount of output token to receive |
| amount_in_max | `u128` | Maximum amount of input token to spend |

### swap_exact_out_2pools
```rust
public entry fun swap_exact_out_2pools(
    sender: &signer,
    pools: vector<address>,
    token_out: Object<Metadata>,
    amount_out: u128,
    amount_in_max: u128,
)
```
Executes a swap through two pools to receive an exact amount of output token.

| Parameter | Type | Description |
|-----------|------|-------------|
| sender | `&signer` | Signer reference of the sender |
| pools | `vector<address>` | Vector of two pool addresses |
| token_out | `Object<Metadata>` | Output token |
| amount_out | `u128` | Exact amount of output token to receive |
| amount_in_max | `u128` | Maximum amount of input token to spend |

## Important Notes
1. All swap functions require one of the tokens to be EDS
2. Multi-pool swaps use EDS as an intermediate token
3. Liquidity addition functions automatically calculate optimal amounts
4. All amounts are specified in the smallest unit of the respective token
