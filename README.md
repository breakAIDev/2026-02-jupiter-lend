# Jupiter Lend audit details
- Total Prize Pool: $107,000 in USDC
    - HM awards: up to $96,000 in USDC
        - If no valid Highs or Mediums are found, the HM pool is $0
    - QA awards: $4,000 in USDC
    - Judge awards: $6,500 in USDC
    - Scout awards: $500 in USDC
- [Read our guidelines for more details](https://docs.code4rena.com/competitions)
- Starts February 12, 2026 20:00 UTC
- Ends March 13, 2026 20:00 UTC

### ❗ Important notes for wardens
1. Since this audit includes live/deployed code, **all submissions will be treated as sensitive**:
    - Wardens are encouraged to submit High-risk submissions affecting live code promptly, to ensure timely disclosure of such vulnerabilities to the sponsor and guarantee payout in the case where a sponsor patches a live critical during the audit.
    - Submissions will be hidden from all wardens (SR and non-SR alike) by default, to ensure that no sensitive issues are erroneously shared.
    - If the submissions include findings affecting live code, there will be no post-judging QA phase. This ensures that awards can be distributed in a timely fashion, without compromising the security of the project. (Senior members of C4 staff will review the judges’ decisions per usual.)
    - By default, submissions will not be made public until the report is published.
    - Exception: if the sponsor indicates that no submissions affect live code, then we’ll make submissions visible to all authenticated wardens, and open PJQA to verified wardens per the usual C4 process.
    - [The "live criticals" exception](https://docs.code4rena.com/awarding#the-live-criticals-exception) therefore applies.
2. Judging phase risk adjustments (upgrades/downgrades):
    - High- or Medium-risk submissions downgraded by the judge to Low-risk (QA) will be ineligible for awards.
    - Upgrading a Low-risk finding from a QA report to a Medium- or High-risk finding is not supported.
    - As such, wardens are encouraged to select the appropriate risk level carefully during the submission phase.

## Publicly known issues

_Anything included in this section is considered a publicly known issue and is therefore ineligible for awards._

### Known DoS Risk

If a transaction loads more than 64 accounts, per current architecture of protocol, the `Liquidation` program and its operation may be DoS'd. (See Zenith Audit Exhibit H1)

### Token Extension Incompatibility

Token extensions support can break protocol. (See Zenith Audit Exhibit M5)

### Rent Claim Issue

There is currently no way to close a position PDA to claim the rent back.

### First / Last Vault User Issues

Issues related to the first and last user of the `Vault` are out-of-scope as we are aware of concerns around that and we intend to create a forever-locked dust position on each market.

### Phantom debt creation

It is possible for user to create a phantom debt position (near min debt bounds) where the debt is not counted in the tick, but exists on position. But as tick acknowledges this debt during position interaction, so there is no effect on the working on the protocol.

# Overview

A two layer modular architecture that enhances capital efficiency, scalability, and risk management.

## Architecture

Jupiter Lend implements a **two-layer modular architecture** that separates liquidity management from user-facing operations, enabling unified liquidity across multiple protocols.

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                          User Layer                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │
│  │   Wallets   │  │    dApps    │  │ Liquidators │              │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘              │
└─────────┼─────────────────┼─────────────────┼───────────────────┘
          │                 │                 │
          ▼                 ▼                 ▼
┌──────────────────────────────────────────────────────────────────┐
│                      Protocol Layer                              │
│  ┌──────────────────┐         ┌──────────────────┐               │
│  │  Lending Protocol│         │  Vaults Protocol │               │
│  │                  │         │                  │               │
│  │  • Deposit       │         │  • Operate       │               │
│  │  • Withdraw      │         │  • Liquidate     │               │
│  │  • Rebalance     │         │  • Rebalance     │               │
│  └────────┬─────────┘         └────────┬─────────┘               │
│           │         ┌──────────────────┘                         │
│           │         │     CPI Calls (Cross-Program Invocations)  │
└───────────┼─────────┼────────────────────────────────────────────┘
            │         │
            ▼         ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                      Liquidity Layer                                     │
│  ┌────────────────────────────────────────────────────────────────────┐  │
│  │          Unified Liquidity Pool (Single Orderbook)                 │  │
│  │                                                                    │  │
│  │  • Operate (Supply, Withdraw, Borrow, Payback)                     │  │
│  │  • Unified liquidity management, token limits and rates management.│  │
│  │  • Atomic transaction execution                                    │  │
│  └────────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────┘
            ▲                              ▲
            │                              │
     ┌──────┴──────┐              ┌────────┴────────┐
     │   Oracle    │              │   Flash Loan    │
     │   Program   │              │    Program      │
     └─────────────┘              └─────────────────┘
```

### Key Benefits

- **Unified Liquidity**: All protocols share the same liquidity pool, maximizing capital efficiency and reducing fragmentation
- **Discrete Risk Framework**: Each protocol (Lending, Vaults) maintains a distinct set of risk parameters (CF, LT, etc) while leveraging shared liquidity within set limits
- **Modular Design**: New protocols can be added without modifying the core liquidity layer

## Repository Structure

```
2026-02-jupiter-lend/
├── programs/                       # Solana programs
│   ├── liquidity/                  # Core liquidity layer
│   ├── lending/                    # Lending protocol
│   ├── vaults/                     # Collateralized borrow protocol
│   ├── oracle/                     # Oracle that utilizes multiple oracles
│   ├── flashloan/                  # Flash loan functionality
│   └── lending_reward_rate_model/  # Reward distribution
├── __tests__/                      # Test suite
├── temp/                           # Build artifacts
└── Anchor.toml                     # Anchor configuration
```

## Program Overview

### Liquidity

The Liquidity program serves as the foundational single-pool architecture that manages all liquidity within the system. It tracks the deposit and borrow positions of integrated programs and enforces program-specific borrowing and withdrawal controls, including rate-limited mechanisms such as debt ceilings and withdrawal limits. Access to this program is permission-based, and end users never interact with it directly. Only whitelisted programs, such as Vaults and Lending, can interact with it through CPIs. All other programs in the ecosystem are built on top of this liquidity infrastructure.

**Key Method**

- **Operate:** The core interaction method that handles all liquidity operations (supply, withdraw, borrow, payback) in a single atomic transaction.

### Lending

The Lending program provides a straightforward lending protocol that offers users direct exposure to yield generation. Users can supply assets to earn interest, making it the core yield-bearing mechanism within the protocol suite.

**Key Methods:**

- **Deposit:** Allows users to supply assets into the protocol
- **Withdraw:** Enables users to withdraw their supplied assets
- **Rebalance:** Synchronizes the protocol's accounting with its actual position on the Liquidity layer, ensuring accurate asset tracking and exchange rates. When rewards are active, rebalance syncs the orderbook to incorporate accrued rewards

### Vaults

The Vaults program implements a collateralized debt position (CDP) system. Users deposit collateral assets to borrow debt tokens against them, enabling leverage and capital efficiency. This program handles collateral management, debt issuance, and liquidation mechanisms through a tick-based architecture for efficient risk management.

**Key Methods:**

- **Operate:** Manages collateral and debt positions by interacting with the Liquidity layer, handling deposits, withdrawals, borrows, and paybacks
- **Liquidate:** Allows liquidators to repay debt on behalf of risky positions in exchange for collateral at a discount (liquidation penalty). Positions become liquidatable when they exceed the liquidation threshold (e.g., 90% LT). The liquidation mechanism operates on a tick-based system, where positions are liquidated based on their risk level. Positions with ratio above the liquidation max limit (e.g., 95% LML) is automatically absorbed by the protocol
- **Rebalance:** Reconciles vault positions with the underlying Liquidity layer to maintain accurate collateral and debt accounting. When rewards are active on the protocol, rebalance syncs the orderbook to account for accrued rewards

### Oracle

The Oracle program provides reliable price feeds for the protocol. It supports data sources from multiple providers including Pyth, Chainlink, and Solana-native pools to ensure accurate asset pricing.

### Flash Loan

The Flash Loan program provides atomic loans that must be borrowed and repaid within a same transaction. This enables arbitrage, liquidations, and other advanced DeFi operations without requiring upfront capital.

### Lending Reward Rate Model

The Lending Reward Rate Model program manages the calculation and distribution of rewards for the Lending protocol, determining reward rates based on protocol parameters and market conditions.


## Links

- **Previous audits:** 
  - [Zenith Report](https://github.com/jup-ag/docs/blob/main/static/files/audits/lend-zenith.pdf)
  - [Ottersec Report 1](https://github.com/jup-ag/docs/tree/main/static/files/audits/lend-ottersec.pdf)
  - [Ottersec Report 2](https://github.com/jup-ag/docs/tree/main/static/files/audits/lend-ottersec-2.pdf)
  - [Offside Labs Oracle / Flashloan Report](https://github.com/jup-ag/docs/blob/main/static/files/audits/lend-oracle-and-flashloan-offside.pdf)
  - [Offside Labs Vault Report](https://github.com/jup-ag/docs/blob/main/static/files/audits/lend-vault-offside.pdf)
  - [Offside Labs Liquidity Report](https://github.com/jup-ag/docs/tree/main/static/files/audits/lend-liquidity-offside.pdf)
  - [Mixbytes Vault Report](https://github.com/jup-ag/docs/blob/main/static/files/audits/lend-vault-mixbytes.pdf)
- **Documentation:** https://fluid.guides.instadapp.io
- **Website:** https://jup.ag/lend/earn
- **X/Twitter:** https://x.com/jup_lend

---

# Scope

### Files in scope

**Note:** The nSLoC counts in the following table have been automatically generated and may differ depending on the definition of what a "significant" line of code represents. As such, they should be considered indicative rather than absolute representations of the lines involved in each contract.

| File   | nSLOC |
| ------ | ----- |
|[crates/library/src/errors.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/errors.rs)| 31 |
|[crates/library/src/lib.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/lib.rs)| 4 |
|[crates/library/src/math/bn.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/math/bn.rs)| 488 |
|[crates/library/src/math/casting.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/math/casting.rs)| 34 |     
|[crates/library/src/math/ceil_div.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/math/ceil_div.rs)| 73 |   
|[crates/library/src/math/floor_div.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/math/floor_div.rs)| 120 |
|[crates/library/src/math/merkle.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/math/merkle.rs)| 386 |      
|[crates/library/src/math/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/math/mod.rs)| 8 |
|[crates/library/src/math/safe_math.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/math/safe_math.rs)| 129 |
|[crates/library/src/math/tick.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/math/tick.rs)| 204 |
|[crates/library/src/math/u256.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/math/u256.rs)| 494 |
|[crates/library/src/structs.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/structs.rs)| 16 |
|[crates/library/src/token/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/token/mod.rs)| 2 |
|[crates/library/src/token/spl.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/crates/library/src/token/spl.rs)| 130 |
|[programs/flashloan/src/constants.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/constants.rs)| 7 |
|[programs/flashloan/src/errors.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/errors.rs)| 32 |
|[programs/flashloan/src/events.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/events.rs)| 13 |
|[programs/flashloan/src/invokes/liquidity_layer.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/invokes/liquidity_layer.rs)| 91 |
|[programs/flashloan/src/invokes/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/invokes/mod.rs)| 1 |
|[programs/flashloan/src/lib.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/lib.rs)| 138 |
|[programs/flashloan/src/state/context.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/state/context.rs)| 99 |
|[programs/flashloan/src/state/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/state/mod.rs)| 6 |
|[programs/flashloan/src/state/seeds.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/state/seeds.rs)| 1 |
|[programs/flashloan/src/state/state.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/state/state.rs)| 97 |
|[programs/flashloan/src/validate.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/flashloan/src/validate.rs)| 118 |
|[programs/lending/src/constant.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/constant.rs)| 8 |
|[programs/lending/src/errors.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/errors.rs)| 30 |
|[programs/lending/src/events.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/events.rs)| 42 |
|[programs/lending/src/invokes/f_token.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/invokes/f_token.rs)| 47 |
|[programs/lending/src/invokes/liquidity_layer.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/invokes/liquidity_layer.rs)| 91 |
|[programs/lending/src/invokes/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/invokes/mod.rs)| 4 |
|[programs/lending/src/lib.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/lib.rs)| 95 |
|[programs/lending/src/module/admin.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/module/admin.rs)| 179 |
|[programs/lending/src/module/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/module/mod.rs)| 4 |
|[programs/lending/src/module/user.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/module/user.rs)| 63 |
|[programs/lending/src/state/context.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/state/context.rs)| 370 |
|[programs/lending/src/state/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/state/mod.rs)| 6 |
|[programs/lending/src/state/seeds.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/state/seeds.rs)| 6 |
|[programs/lending/src/state/state.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/state/state.rs)| 28 |
|[programs/lending/src/utils/deposit.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/utils/deposit.rs)| 100 |
|[programs/lending/src/utils/helpers.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/utils/helpers.rs)| 191 |
|[programs/lending/src/utils/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/utils/mod.rs)| 8 |
|[programs/lending/src/utils/rebalance.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/utils/rebalance.rs)| 38 |
|[programs/lending/src/utils/withdraw.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lending/src/utils/withdraw.rs)| 80 |
|[programs/lendingRewardRateModel/src/constants.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lendingRewardRateModel/src/constants.rs)| 8 |
|[programs/lendingRewardRateModel/src/errors.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lendingRewardRateModel/src/errors.rs)| 30 |
|[programs/lendingRewardRateModel/src/events.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lendingRewardRateModel/src/events.rs)| 37 |
|[programs/lendingRewardRateModel/src/invokes/lending.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lendingRewardRateModel/src/invokes/lending.rs)| 46 |
|[programs/lendingRewardRateModel/src/invokes/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lendingRewardRateModel/src/invokes/mod.rs)| 2 |
|[programs/lendingRewardRateModel/src/lib.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lendingRewardRateModel/src/lib.rs)| 137 |
|[programs/lendingRewardRateModel/src/state/context.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lendingRewardRateModel/src/state/context.rs)| 264 |
|[programs/lendingRewardRateModel/src/state/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lendingRewardRateModel/src/state/mod.rs)| 6 |
|[programs/lendingRewardRateModel/src/state/seeds.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lendingRewardRateModel/src/state/seeds.rs)| 2 |
|[programs/lendingRewardRateModel/src/state/state.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/lendingRewardRateModel/src/state/state.rs)| 81 |
|[programs/liquidity/src/constants.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/constants.rs)| 18 |
|[programs/liquidity/src/errors.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/errors.rs)| 90 |
|[programs/liquidity/src/events.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/events.rs)| 106 |
|[programs/liquidity/src/lib.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/lib.rs)| 184 |
|[programs/liquidity/src/module/admin.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/module/admin.rs)| 614 |
|[programs/liquidity/src/module/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/module/mod.rs)| 2 |
|[programs/liquidity/src/module/user.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/module/user.rs)| 249 |
|[programs/liquidity/src/state/context.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/state/context.rs)| 379 |
|[programs/liquidity/src/state/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/state/mod.rs)| 16 |
|[programs/liquidity/src/state/rate_model.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/state/rate_model.rs)| 508 |
|[programs/liquidity/src/state/seeds.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/state/seeds.rs)| 7 |
|[programs/liquidity/src/state/state.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/state/state.rs)| 71 |
|[programs/liquidity/src/state/structs.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/state/structs.rs)| 49 |
|[programs/liquidity/src/state/token_reserve.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/state/token_reserve.rs)| 422 |
|[programs/liquidity/src/state/user_borrow_position.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/state/user_borrow_position.rs)| 194 |
|[programs/liquidity/src/state/user_supply_position.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/state/user_supply_position.rs)| 180 |
|[programs/liquidity/src/utils/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/utils/mod.rs)| 2 |
|[programs/liquidity/src/utils/token.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/liquidity/src/utils/token.rs)| 37 |
|[programs/oracle/src/constants.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/constants.rs)| 21 |
|[programs/oracle/src/errors.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/errors.rs)| 54 |
|[programs/oracle/src/events.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/events.rs)| 15 |
|[programs/oracle/src/helper.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/helper.rs)| 164 |
|[programs/oracle/src/lib.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/lib.rs)| 180 |
|[programs/oracle/src/modules/chainlink.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/modules/chainlink.rs)| 24 |
|[programs/oracle/src/modules/jup_lend.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/modules/jup_lend.rs)| 64 |
|[programs/oracle/src/modules/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/modules/mod.rs)| 14 |
|[programs/oracle/src/modules/msol_pool.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/modules/msol_pool.rs)| 38 |
|[programs/oracle/src/modules/pyth.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/modules/pyth.rs)| 67 |
|[programs/oracle/src/modules/redstone.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/modules/redstone.rs)| 38 |
|[programs/oracle/src/modules/single_pool.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/modules/single_pool.rs)| 121 |
|[programs/oracle/src/modules/stake_pool.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/modules/stake_pool.rs)| 78 |
|[programs/oracle/src/state/context.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/state/context.rs)| 54 |
|[programs/oracle/src/state/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/state/mod.rs)| 9 |
|[programs/oracle/src/state/schema.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/state/schema.rs)| 183 |
|[programs/oracle/src/state/seeds.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/state/seeds.rs)| 2 |
|[programs/oracle/src/state/state.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/state/state.rs)| 18 |
|[programs/oracle/src/state/structs.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/oracle/src/state/structs.rs)| 58 |
|[programs/vaults/src/constants.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/constants.rs)| 29 |
|[programs/vaults/src/errors.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/errors.rs)| 152 |
|[programs/vaults/src/events.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/events.rs)| 153 |
|[programs/vaults/src/invokes/liquidity_layer.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/invokes/liquidity_layer.rs)| 102 |
|[programs/vaults/src/invokes/mint.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/invokes/mint.rs)| 61 |
|[programs/vaults/src/invokes/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/invokes/mod.rs)| 6 |
|[programs/vaults/src/invokes/oracle.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/invokes/oracle.rs)| 53 |
|[programs/vaults/src/lib.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/lib.rs)| 194 |
|[programs/vaults/src/module/admin.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/module/admin.rs)| 556 |
|[programs/vaults/src/module/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/module/mod.rs)| 3 |
|[programs/vaults/src/module/user.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/module/user.rs)| 938 |
|[programs/vaults/src/module/view.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/module/view.rs)| 22 |
|[programs/vaults/src/state/branch.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/branch.rs)| 216 |
|[programs/vaults/src/state/context.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/context.rs)| 693 |
|[programs/vaults/src/state/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/mod.rs)| 22 |
|[programs/vaults/src/state/position.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/position.rs)| 166 |
|[programs/vaults/src/state/seeds.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/seeds.rs)| 11 |
|[programs/vaults/src/state/state.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/state.rs)| 20 |
|[programs/vaults/src/state/structs.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/structs.rs)| 419 |
|[programs/vaults/src/state/tick.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/tick.rs)| 140 |
|[programs/vaults/src/state/tick_has_debt.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/tick_has_debt.rs)| 1109 |
|[programs/vaults/src/state/tick_id_liquidation.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/tick_id_liquidation.rs)| 78 |
|[programs/vaults/src/state/vault_config.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/vault_config.rs)| 22 |
|[programs/vaults/src/state/vault_state.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/state/vault_state.rs)| 313 |
|[programs/vaults/src/utils/common.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/utils/common.rs)| 52 |
|[programs/vaults/src/utils/liquidate.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/utils/liquidate.rs)| 121 |
|[programs/vaults/src/utils/mod.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/utils/mod.rs)| 8 |
|[programs/vaults/src/utils/operate.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/utils/operate.rs)| 214 |
|[programs/vaults/src/utils/validate.rs](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/programs/vaults/src/utils/validate.rs)| 267 |
| **Totals** | **15195** |

*For a machine-readable version, see [scope.txt](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/scope.txt)*

### Files out of scope

| File         |
| ------------ |
|[test-utils/rust/src/\*\*.\*\*](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/test-utils/rust/src)|
|[tests/src/\*\*.\*\*](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/tests/src)|
| Totals: 56 |

*For a machine-readable version, see [out_of_scope.txt](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/out_of_scope.txt)*

# Additional context

## Areas of concern (where to focus for bugs)

`Vault` Protocol: 
- After liquidation the final position of a user should be loaded correctly.
- Absorption and liquidation should work as intended. 
- Branch state should be maintained at all places required, like closed and merged. 

## Main invariants

- Only the owner of an NFT should be able to withdraw or borrow from the protocol. 
- Only whitelisted users on the Liquidity Layer (LL) should be able to interact with it.
- Amounts assigned to claim accounts on the LL should always be available for claiming at any time (reserved)
- Exchange prices should only ever increase.
- No interactions on the extreme sides should be possible, where e.g. 1 is given as the supply amount and leads to unexpected outcomes because of rounding, increasing on a property in storage but not on another data point.
- The protocol must always round in its direction i.e. supply rounds down, borrow rounds up and so on.
- Withdraw and borrow limits must be enforced properly.

## All trusted roles in the protocol

All the child protocols of the LL are permissionless. 

- Vaults
- Lending
- Flashloan 

The LL is a whitelist-based protocol.

## Compilation

### Prerequisites

The codebase of Jupiter Lend represents a suite of Solana programs (i.e. smart contracts) that require a set of specific version-locked compilation programs to be compiled properly.

The following commands have been written for Ubuntu / Linux environments, and Windows users **must** install the Windows-Subsystem-for-Linux (WSL) to be able to compile the codebase and run its tests.

#### Rust

The codebase relies on `rustc` and specifically version `1.81.0`. The version can be installed by setting up the `rustup` toolkit via the following commands:

```bash 
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup default 1.81.0
```

#### Solana CLI

The codebase relies on the Solana CLI and specifically version `2.3.0` which can be installed as follows:

```bash 
sh -c "$(curl -sSfL https://release.anza.xyz/v2.3.0/install)"
```

Additionally, a keypair must be generated for the test suites to function properly:

```bash 
solana-keygen new
```

This keypair will be located in the following path inside your `$HOME` directory: `.config/solana/id.json`

#### Anchor Framework

The `anchor` framework utilized to compile the programs is version `0.32.0` which can be installed through the `avm` version manager as follows:

```bash 
# Install Anchor Version Manager (AVM)
cargo install --git https://github.com/coral-xyz/anchor avm --locked --force

# Install Anchor
avm install 0.32.0
avm use 0.32.0
```

#### NodeJS & `pnpm`

The codebase relies on the NodeJS framework to install its dependencies as well as the `pnpm` toolkit to properly execute post-installation scripts and lock the versions of those dependencies. The versions tested for building the project were `20.9.0` and `10.29.2` respectively. 

NodeJS can be installed via the [official NodeJS website](https://nodejs.org/en) whereas `pnpm` can be installed as follows:

```bash 
npm i -g pnpm
```

#### `bun`

Finally, the codebase relies on a JavaScript runtime / package manager called `bun` which can be installed as follows:

```bash
curl -fsSL https://bun.com/install | bash
```

#### Test Environment Setup

For the test suites of the repository to run properly, the Solana keypair path value must be configured in the `ANCHOR_WALLET_PAHT` variable, f.e. as follows:

```bash 
export ANCHOR_WALLET_PATH=".config/solana/id.json"
```

A Solana RPC endpoint must also be configured for the test suites to run properly. Using the public rate-limited Solana RPC endpoint as an example:

```bash 
export ANCHOR_PROVIDER_MAINNET_URL="https://api.mainnet.solana.com"
```

**This is an example value, and trying to run test suites with the rate-limited public end point will fail due to the many requests performed**. Wardens are advised to utilize their own RPC endpoint that is unrestricted.

### Building

After all tools have been installed, the NodeJS dependencies of the project must be installed and setup via the following command:

```bash 
pnpm i
```

Once the command finishes successfully, all programs of the codebase are ready to be built as follows:

```bash
pnpm build
```

Both the `build` and `test` commands accept an optional `--program` argument that permits the compilation / testing of a single program in the repository. The available options for the `--program` flag can be observed via the `--list-program` flag.

### Running Tests

Tests can be executed via the following command:

```bash
pnpm test
```

## Miscellaneous

Employees of Jupiter Lend and employees' family members are ineligible to participate in this audit.

Code4rena's rules cannot be overridden by the contents of this README. In case of doubt, please check with C4 staff.
