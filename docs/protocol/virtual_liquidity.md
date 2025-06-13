---
title: Liquidity
parent: Protocol
nav_order: 5
---

# Virtual Liquidity

## 1. Core Concepts

### 1.1 Total Reserves
The total reserves (TR) in a pool consist of real reserves (R) and virtual reserves (VR):

$$ TR = R + VR $$

Where virtual reserves are calculated as:
$$ VR = R \times (m - 1) $$
- m is the multiplier (1-100)

## 2. Trading Impact Analysis

### 2.1 Price Calculation
The price (P) in the pool is determined by the ratio of total reserves:

$$ P = \frac{TR_1}{TR_0} = \frac{R_1 + VR_1}{R_0 + VR_0} $$

### 2.2 Output Amount Calculation
For a given input amount (amount_in), the output amount (amount_out) is calculated as:

$$ amount\_out = \frac{amount\_in \times TR_1}{amount\_in + TR_0} $$

## 3. Mathematical Implementation

### 3.1 Reserve Calculation
```move
let reserves_0_total = reserves_0 + virtual_total_0;
let reserves_1_total = reserves_1 + virtual_total_1;
```

### 3.2 Output Amount Calculation
```move
let amount_out = math128::mul_div(
    amount_in, 
    total_reserver_out, 
    amount_in + total_reserver_in
);
```

## 4. Price Impact Analysis

### 4.1 Price Impact Formula
The price impact (PI) is calculated as:

$$ PI_{bps} = \frac{(TR_{in} + amount\_in) \times TR_{out}}{(TR_{out} - amount\_out) \times TR_{in}} \times 10000 - 10000 $$

Where:
- TR_in: Total reserve of input token
- TR_out: Total reserve of output token

### 4.2 Implementation
```move
let pi_bps = (BPS_10000 as u256) * 
    ((tr_in as u256) + (amount_in as u256)) * 
    (tr_out as u256) / 
    ((((tr_out as u256) - (amount_out as u256)) * 
    (tr_in as u256))) - 
    (BPS_10000 as u256);
```

## 5. Virtual Liquidity Effects

### 5.1 Price Stability
The price change (ΔP) for a given trade is reduced by virtual liquidity:

$$ \Delta P = \frac{P_{after} - P_{before}}{P_{before}} $$

Where:
$$ P_{before} = \frac{TR_1}{TR_0} $$
$$ P_{after} = \frac{TR_1 - amount\_out}{TR_0 + amount\_in} $$

### 5.2 Slippage Reduction
The slippage (S) is reduced by the multiplier:

$$ S = \frac{1}{m} \times S_{base} $$

Where S_base is the slippage without virtual liquidity.

## 6. Trading Examples

### 6.1 Small Trade
For a small trade relative to total reserves:

$$ amount\_out \approx amount\_in \times \frac{TR_1}{TR_0} $$

### 6.2 Large Trade
For a large trade, the impact is reduced by virtual liquidity:

$$ amount\_out = \frac{amount\_in \times TR_1}{amount\_in + TR_0} \times (1 - \frac{amount\_in}{TR_0 + VR_0}) $$

## 7. Implementation Details

### 7.1 Swap Calculation
```move
public fun get_amount_out(
    pool: address,
    token_in: Object<Metadata>,
    amount_in: u128,
): (u128, u128) {
    let (amount_out, dao_fee, base_fee, dynamic_fee) = 
        get_amount_out_and_fees(pool, token_in, amount_in);
    
    let fee = dao_fee + base_fee + dynamic_fee;
    (amount_out, fee)
}
```

### 7.2 Reserve Updates
```move
fun update_reserves_with_liquidity(
    pool: address,
    liquidity_token_amount: u128,
    fungible_asset_0: FungibleAsset,
    fungible_asset_1: FungibleAsset,
) {
    let amount_0 = fungible_asset::amount(&fungible_asset_0);
    let amount_1 = fungible_asset::amount(&fungible_asset_1);
    
    if (amount_0 > 0 && amount_1 > 0) {
        add_pair_virtual_reserve(
            pool, 
            amount_0 * (multipler - 1), 
            amount_1 * (multipler - 1)
        );
    }
}
```

## 8. Optimization Strategies

### 8.1 Optimal Trade Size
The optimal trade size (OTS) considering virtual liquidity:

$$ OTS = \sqrt{\frac{TR_0 \times TR_1}{2 \times m}} $$

### 8.2 Price Impact Minimization
To minimize price impact:

$$ \min(PI) = \min(\frac{amount\_in}{TR_0 + VR_0}) $$

## 9. Economic Implications

### 9.1 Capital Efficiency
The capital efficiency (CE) is enhanced by virtual liquidity:

$$ CE = \frac{Trading\_Volume}{Real\_Liquidity} \times m $$

### 9.2 Market Depth
The effective market depth (MD) is increased:

$$ MD = Real\_Liquidity \times m $$

## 10. Implementation Considerations

### 10.1 Precision Handling
All calculations use basis points for precision:
- 1 bps = 0.01%
- 10000 bps = 100%

### 10.2 Rounding Rules
The protocol uses ceiling division for output amounts:

$$ amount\_out_{final} = \lceil amount\_out_{calculated} \rceil $$

This mathematical analysis demonstrates how virtual liquidity affects trading in EdenSwap, showing the relationship between input and output amounts and how the multiplier system enhances price stability and trading efficiency.
