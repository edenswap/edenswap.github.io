---
title: Protocol
nav_order: 2
---
# EdenSwap Protocol Overview

## Concept and Innovation

EdenSwap is a decentralized exchange (DEX) protocol built on the Move language, inspired by Uniswap V3. Its most innovative feature is the virtual liquidity system, which enhances price stability without requiring additional capital. The protocol implements a multiplier-based mechanism (range 1-100) that creates virtual reserves proportional to actual liquidity, effectively reducing price impact and improving trading efficiency. This unique approach allows the protocol to maintain better price stability while maximizing capital efficiency.

## Fee Structure and Economic Model

The protocol implements a sophisticated three-tier fee structure consisting of base fees, DAO fees, and dynamic fees. The base fee is a standard trading fee, while the DAO fee contributes to protocol governance. The dynamic fee adjusts based on pool imbalance and the multiplier value, creating an adaptive fee system that responds to market conditions. This fee structure ensures sustainable protocol revenue while maintaining competitive trading costs. The system also includes mechanisms for fee collection and distribution, with clear separation between protocol fees and liquidity provider rewards.

## Trading and Liquidity Management

EdenSwap supports both single-sided and paired liquidity provision, with enhanced features for liquidity providers. The protocol's virtual liquidity system reduces impermanent loss and improves capital efficiency for liquidity providers. Trading is optimized through the virtual liquidity mechanism, which reduces price impact and slippage. The protocol supports multi-hop routing for complex trades and implements sophisticated price impact calculations that consider both real and virtual reserves. This creates a more efficient trading environment with better price stability and reduced trading costs.

## Governance and Security

The protocol implements a manager-based governance system that controls critical parameters while maintaining security and efficiency. The governance structure allows for parameter updates, emergency controls, and fee management. Security is ensured through strict access controls, parameter constraints, and emergency pause functionality. The system includes comprehensive event tracking for transparency and accountability. This governance framework provides a balance between efficient protocol management and security, ensuring the protocol can adapt to market conditions while maintaining user trust and protocol integrity.
