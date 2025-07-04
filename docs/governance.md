---
title: Governance
nav_order: 4
---
# Governance

## Manager-Based Governance Structure

Thee governance system in EdenSwap implements a manager-based approach, where a designated manager address holds the authority to control critical protocol parameters. This centralized yet controlled governance model ensures efficient decision-making while maintaining protocol security. The manager can adjust key parameters such as the DAO fee rate, pool multipliers, and emergency controls. The system implements strict access controls and parameter constraints to prevent unauthorized modifications and ensure protocol stability.

## Governance Implementation and Parameter Management

The governance implementation is built around the `LiquidityPoolConfigs` structure, which stores all configurable parameters including the manager address, fee recipient, and various fee rates. All governance functions require manager authorization, enforced through strict checks like `assert!(address_of(sender) == configs.manager, ENOT_AUTHORIZED)`. The system includes parameter constraints (e.g., multiplier range of 1-100) and emits events for all governance actions to ensure transparency. The manager can update critical parameters such as the DAO fee rate through the `set_fee` function, adjust pool multipliers via `update_pool_multipler`, and control emergency situations using the `set_pause` function.

## Security and Fee Management

The governance system incorporates robust security measures, including parameter validation and emergency controls. The fee management system enables the collection and distribution of protocol fees to the designated recipient through the `claim_fees` function. All governance actions are tracked through event emissions, providing transparency and accountability. The system maintains a balance between efficient protocol management and security, with clear parameter constraints and access controls. This structure ensures that protocol updates are executed safely while maintaining the integrity of the EdenSwap ecosystem.
