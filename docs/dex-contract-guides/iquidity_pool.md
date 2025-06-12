---
title: Liquidity pool
parent: Contract guides
nav_order: 2
---

## Read-Only Functions
```rust
// Null
```


## State-Changing Functions

### all_pools

```
public fun all_pools(): vector<address> 
```

Get all pool addresses.



### pool_info
#### data structure:
``` 
 struct PoolInfo {
    pool: address,
    token_0: address,
    token_1: address,

    dao_fee_bps: u128,
    base_swap_fee_bps: u128,
    multipler: u128,

    reserve_0: u128,
    reserve_1: u128,

    virtual_token_reserve_0: u128,
    virtual_token_reserve_1: u128,

    virtual_pairing_reserve_0: u128,
    virtual_pairing_reserve_1: u128,

    total_reserve_0: u128,
    total_reserve_1: u128,

    lp_token_supply: u128,
    lp_token_decimals: u8,
}
```
#### funtion:
```
public fun pool_info(pool: address): PoolInfo
```

Get pool detail state of pool. PoolInfo described blow:

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
| lp_token_decimals         | u8      | Deciams of LP token .                                  |



### get_amount_in

```
public fun get_amount_in(
    pool: address,
    token_out: Object<Metadata>,
    amount_out: u128,
): (u128, u128)
```

Giving `pool` , `token_out`, `amount_out`, then calculate amount of input token needed and amount of  fee to pay.



### get_amount_out

```
public fun get_amount_out(
    pool: address,
    token_in: Object<Metadata>,
    amount_in: u128,
): (u128, u128)
```

Giving `pool` , `token_in`, `amount_in`, then calculate amount of output token needed and amount of  fee to pay.

