---
title: Liquidity pool
parent: Contract guides
nav_order: 2
---

# Liquidity Pool

The Liquidity Pool contract is a core component of EdenSwap that manages token pairs, liquidity provision, and swap operations. It implements a constant product formula (x * y = k) with virtual liquidity support.

## Overview

The Liquidity Pool contract provides the following key functionalities:
- Pool creation and management
- Liquidity provision and removal
- Token swaps with dynamic fee calculation
- Virtual liquidity management
- LP token minting and burning

## Data Structures

### PoolInfo
```rust
struct PoolInfo {
    pool: address,                    // Pool address
    token_0: address,                 // First token address
    token_1: address,                 // Second token address
    dao_fee_bps: u128,               // DAO fee rate in basis points
    base_swap_fee_bps: u128,         // Base swap fee rate in basis points
    multipler: u128,                 // Virtual liquidity multiplier
    reserve_0: u128,                 // Token 0 reserve amount
    reserve_1: u128,                 // Token 1 reserve amount
    virtual_token_reserve_0: u128,   // Token 0 pure virtual amount
    virtual_token_reserve_1: u128,   // Token 1 pure virtual amount
    virtual_pairing_reserve_0: u128, // Token 0 pairing virtual amount
    virtual_pairing_reserve_1: u128, // Token 1 pairing virtual amount
    total_reserve_0: u128,           // Total reserve 0 (real + virtual)
    total_reserve_1: u128,           // Total reserve 1 (real + virtual)
    lp_token_supply: u128,           // LP token total supply
    lp_token_decimals: u8,           // LP token decimals
}
```

## Read-Only Functions

### Pool Information

#### `all_pools`
```rust
public fun all_pools(): vector<address>
```
Returns a list of all pool addresses in the system.

#### `pool_info`
```rust
public fun pool_info(pool: address): PoolInfo
```
Returns detailed information about a specific pool.

| Name                      | Type    | Description                                            |
| ------------------------- | ------- | ------------------------------------------------------ |
| pool                      | address | Pool address.                                          |
| token_0                   | address | One token of pool                                      |
| token_1                   | address | One token of pool                                      |
| dao_fee_bps               | u128    | Swap fee rate in bps send to DAO address.              |
| base_swap_fee_bps         | u128    | Swap fee rate in bps send to pool address.             |
| multipler                 | u128    | 1 - no virtual liquidity; >= 2 - has virtual liquidity |
| reserve_0                 | u128    | token_0 reserve amount                                 |
| reserve_1                 | u128    | token_1 reserve amount                                 |
| virtual_token_reserve_0   | u128    | token_0 pure virtual amount                            |
| virtual_token_reserve_1   | u128    | token_1 pure virtual amount                            |
| virtual_pairing_reserve_0 | u128    | token_0 pairing virtual amount                         |
| virtual_pairing_reserve_1 | u128    | token_1 pairing virtual amount                         |
| total_reserve_0           | u128    | Sum of reserve and virtual amounts of token_0          |
| total_reserve_1           | u128    | Sum of reserve and virtual amounts of token_1          |
| lp_token_supply           | u128    | LP token total supply.                                 |
| lp_token_decimals         | u8      | Decimals of LP token.                                  |

#### `pool_tokens_metadata`
```rust
public fun pool_tokens_metadata(pool_addr: address): (TokenInfo, TokenInfo)
```
Returns metadata for both tokens in the pool.

| Name      | Type    | Description                |
| --------- | ------- | -------------------------- |
| pool_addr | address | The address of the pool    |

Returns:
- `(TokenInfo, TokenInfo)`: Metadata for both tokens in the pool

#### `lp_token_balance`
```rust
public fun lp_token_balance(pool: address, provider: address): u128
```
Returns the LP token balance for a specific provider in a pool.

| Name      | Type    | Description                    |
| --------- | ------- | ------------------------------ |
| pool      | address | The address of the pool        |
| provider  | address | The address of the provider    |

Returns:
- `u128`: The LP token balance

### Swap Calculations

#### `get_amount_out`
```rust
public fun get_amount_out(
    pool: address,
    token_in: Object<Metadata>,
    amount_in: u128,
): (u128, u128)
```
Calculates the output amount and fees for a given input amount.

| Name      | Type              | Description                    |
| --------- | ----------------- | ------------------------------ |
| pool      | address           | The address of the pool        |
| token_in  | Object<Metadata>  | The metadata of input token    |
| amount_in | u128              | The amount of input token      |

Returns:
- `(u128, u128)`: Tuple containing (output amount, fee amount)

#### `get_amount_in`
```rust
public fun get_amount_in(
    pool: address,
    token_out: Object<Metadata>,
    amount_out: u128,
): (u128, u128)
```
Calculates the required input amount and fees for a desired output amount.

| Name       | Type              | Description                     |
| ---------- | ----------------- | ------------------------------- |
| pool       | address           | The address of the pool         |
| token_out  | Object<Metadata>  | The metadata of output token    |
| amount_out | u128              | The desired output amount       |

Returns:
- `(u128, u128)`: Tuple containing (input amount, fee amount)

### Virtual Liquidity

#### `pool_virtual_token_reserves`
```rust
public fun pool_virtual_token_reserves(pool: address): (u128, u128)
```
Returns the pure virtual reserves for both tokens.

| Name | Type    | Description             |
| ---- | ------- | ----------------------- |
| pool | address | The address of the pool |

Returns:
- `(u128, u128)`: Tuple containing (token0 virtual reserve, token1 virtual reserve)

#### `pool_virtual_pairing_reserves`
```rust
public fun pool_virtual_pairing_reserves(pool: address): (u128, u128)
```
Returns the pairing virtual reserves for both tokens.

| Name | Type    | Description             |
| ---- | ------- | ----------------------- |
| pool | address | The address of the pool |

Returns:
- `(u128, u128)`: Tuple containing (token0 pairing reserve, token1 pairing reserve)

## State-Changing Functions

### Virtual Liquidity Management

#### `set_pool_multipler`
```rust
public entry fun set_pool_multipler(
    sender: &signer,
    pool_addr: address,
    multipler: u128,
)
```
Updates the virtual liquidity multiplier for a pool.

| Name      | Type    | Description                                |
| --------- | ------- | ------------------------------------------ |
| sender    | &signer | Signer reference of the manager            |
| pool_addr | address | The address of the pool                    |
| multipler | u128    | New multiplier value (must be >= 1)        |

## Events

### `CreatePool`
Emitted when a new pool is created.
```rust
struct CreatePool has drop, store {
    pool: address,
    token_0: address,
    token_1: address,
    multiplier: u128,
}
```

### `MintLP`
Emitted when LP tokens are minted.
```rust
struct MintLP has drop, store {
    pool: address,
    provider: address,
    token_0: address,
    token_1: address,
    amount_0: u128,
    amount_1: u128,
    lp_token_amount: u128,
}
```

### `BurnLP`
Emitted when LP tokens are burned.
```rust
struct BurnLP has drop, store {
    pool: address,
    provider: address,
    token_0: address,
    token_1: address,
    redeem_amount_0: u128,
    redeem_amount_1: u128,
    lp_token_amount: u128,
}
```

### `ReservesUpdated`
Emitted when pool reserves are updated.
```rust
struct ReservesUpdated has drop, store {
    pool: address,
    reserve_0: u128,
    reserve_1: u128,
    total_reserve_0: u128,
    total_reserve_1: u128,
}
```

## Important Notes

1. All amounts are specified in the smallest unit of the respective token.
2. The virtual liquidity multiplier affects the pool's effective liquidity.
3. LP token transfers must be done through the contract's transfer function.
4. One of the tokens in each pool must be EDS.
5. The base swap fee is 0.1% (10 bps) and the DAO fee is 0.09% (9 bps).
6. Virtual liquidity can be enabled by setting a multiplier >= 2.
7. All swap operations require one of the tokens to be EDS.