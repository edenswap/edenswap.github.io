---
title: Fees
parent: Protocol
nav_order: 5
---

# Fee Mechanism 

## 1. Fee Structure Overview

The total fee (F) in EdenSwap consists of three components:

$$ F_{total} = F_{base} + F_{dao} + F_{dynamic} $$

## 2. Individual Fee Components

### 2.1 Base Fee
The base fee is a fixed percentage of the trade amount:

$$ F_{base} = amount \times \frac{base\_fee\_bps}{10000}$$

Where:
- `base_fee_bps` is the base fee in basis points (e.g., 30 bps = 0.3%)
- `amount` is the input amount for the trade

### 2.2 DAO Fee
The DAO fee is calculated similarly:

$$ F_{dao} = amount \times \frac{dao\_fee\_bps}{10000} $$

Where:
- `dao_fee_bps` is the DAO fee in basis points

### 2.3 Dynamic Fee
The dynamic fee is more complex and depends on pool imbalance:

$$ F_{dynamic} = amount \times \frac{dynamic\_fee\_bps}{10000} $$

Where `dynamic_fee_bps` is calculated as:

$$ dynamic\_fee\_bps = \begin{cases}
BASE\_SWAP\_FEES\_BPS \times (multipler - 1) \times (2 \times \frac{10000}{10000 + propotion\_bps} - 1) & \text{if } propotion\_bps < NEED\_DYNAMIC\_FEE\_RATE\_BPS \\
0 & \text{otherwise}
\end{cases} $$

## 3. Pool Imbalance Calculation

The proportion after swap is calculated as:

$$ propotion\_bps = \frac{(reserve\_out - amount\_out) \times (total\_reserve\_in + amount\_in)}{(reserve\_in + amount\_in) \times (total\_reserve\_out - amount\_out)} \times 10000 $$

Where:
- `reserve_in/out`: Current pool reserves
- `total_reserve_in/out`: Total reserves including virtual liquidity
- `amount_in/out`: Trade amounts

## 4. Output Amount Calculation

The final output amount after fees:

$$ amount\_out = \begin{cases}
amount\_out - F_{total} & \text{if } token\_in == EDS \\
amount\_out \times \frac{10000 - fixed\_fee\_rate - dynamic\_fee\_bps}{10000} & \text{otherwise}
\end{cases} $$

## 5. Mathematical Implementation

### 5.1 Fee Calculation Function
```move
fun calc_dynamic_fee_bps(
    amount_in: u128,
    amount_out: u128,
    reserve_in: u128,
    reserve_out: u128,
    total_reserver_in: u128,
    total_reserver_out: u128,
    multipler: u128,
): u128 {
    // Calculate proportion after swap
    let propotion_after_swap_bps = calc_propotion_after_swap_bps(
        amount_in,
        amount_out,
        reserve_in,
        reserve_out,
        total_reserver_in,
        total_reserver_out
    );

    // Calculate dynamic fee
    let dynamic_fee_bps = if (propotion_after_swap_bps < NEED_DYNAMIC_FEE_RATE_BPS) {
        BASE_SWAP_FEES_BPS * (multipler - 1) * 
        (2 * BPS_10000 / (BPS_10000 + propotion_after_swap_bps) - 1)
    } else {
        0
    };

    dynamic_fee_bps
}
```

### 5.2 Proportion Calculation
```move
fun calc_propotion_after_swap_bps(
    amount_in: u128,
    amount_out: u128,
    reserve_in: u128,
    reserve_out: u128,
    total_reserver_in: u128,
    total_reserver_out: u128,
): u128 {
    let propotion_after_swap_bps = if (amount_out <= reserve_out) {
        ((BPS_10000 as u256) * 
        ((reserve_out - amount_out) as u256) * 
        ((total_reserver_in + amount_in) as u256)) /
        (((reserve_in + amount_in) as u256) * 
        ((total_reserver_out - amount_out) as u256))
    } else {
        0
    };

    (propotion_after_swap_bps as u128)
}
```

## 6. Fee Impact Analysis

### 6.1 Price Impact
The price impact (PI) is calculated as:

$$ PI_{bps} = \frac{(total\_reserve\_in + amount\_in) \times total\_reserve\_out}{(total\_reserve\_out - amount\_out) \times total\_reserve\_in} \times 10000 - 10000 $$

### 6.2 Effective Fee Rate
The effective fee rate (EFR) considering all components:

$$ EFR = \frac{base\_fee\_bps + dao\_fee\_bps + dynamic\_fee\_bps}{10000} $$

## 7. Dynamic Fee Behavior

### 7.1 Multiplier Impact
The multiplier (m) affects the dynamic fee:

$$ F_{dynamic} \propto (m - 1) $$

Where m is the pool multiplier (1-100)

### 7.2 Pool Imbalance Impact
The dynamic fee increases with pool imbalance:

$$ F_{dynamic} \propto \frac{1}{1 + \frac{propotion\_bps}{10000}} $$

## 8. Optimization Considerations

### 8.1 Optimal Trade Size
For a given pool state, the optimal trade size (OTS) can be calculated as:

$$ OTS = \sqrt{\frac{reserve\_in \times reserve\_out}{2 \times EFR}} $$

### 8.2 Fee Minimization
To minimize fees, traders should consider:

$$ \min(F_{total}) = \min(F_{base} + F_{dao} + F_{dynamic}) $$

Subject to:
- Pool constraints
- Price impact limits
- Available liquidity

## 9. Implementation Notes

### 9.1 Fee Calculation Precision
All calculations use basis points (bps) for precision:
- 1 bps = 0.01%
- 100 bps = 1%
- 10000 bps = 100%

### 9.2 Rounding Considerations
The protocol uses ceiling division for fee calculations:

$$ F_{rounded} = \lceil F_{exact} \rceil $$

This ensures protocol safety and prevents rounding exploits.

This mathematical analysis provides a comprehensive understanding of the fee mechanism in EdenSwap, showing how different components interact and affect trading outcomes.
