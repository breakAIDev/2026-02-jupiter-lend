# Sponsorname audit details
- Total Prize Pool: XXX XXX USDC (Airtable: Total award pool)
    - HM awards: up to XXX XXX USDC (Airtable: HM (main) pool)
        - If no valid Highs or Mediums are found, the HM pool is $0 (🐺 C4 EM: adjust in case of tiered pools)
    - QA awards: XXX XXX USDC (Airtable: QA pool)
    - Judge awards: XXX XXX USDC (Airtable: Judge Fee)
    - Scout awards: $500 USDC (Airtable: Scout fee - but usually $500 USDC)
    - (this line can be removed if there is no mitigation) Mitigation Review: XXX XXX USDC
- [Read our guidelines for more details](https://docs.code4rena.com/competitions)
- Starts XXX XXX XX 20:00 UTC (ex. `Starts March 22, 2023 20:00 UTC`)
- Ends XXX XXX XX 20:00 UTC (ex. `Ends March 30, 2023 20:00 UTC`)

### ❗ Important notes for wardens
(🐺 C4 staff: delete the PoC requirement section if not applicable - i.e. for non-Solidity/EVM audits.)
1. A coded, runnable PoC is required for all High/Medium submissions to this audit. 
    - This repo includes a basic template to run the test suite.
    - PoCs must use the test suite provided in this repo.
    - Your submission will be marked as Insufficient if the POC is not runnable and working with the provided test suite.
    - Exception: PoC is optional (though recommended) for wardens with signal ≥ 0.4.
1. Judging phase risk adjustments (upgrades/downgrades):
    - High- or Medium-risk submissions downgraded by the judge to Low-risk (QA) will be ineligible for awards.
    - Upgrading a Low-risk finding from a QA report to a Medium- or High-risk finding is not supported.
    - As such, wardens are encouraged to select the appropriate risk level carefully during the submission phase.

## V12 findings (🐺 C4 staff: remove this section for non-Solidity/EVM audits)

[V12](https://v12.zellic.io/) is [Zellic](https://zellic.io)'s in-house AI auditing tool. It is the only autonomous Solidity auditor that [reliably finds Highs and Criticals](https://www.zellic.io/blog/introducing-v12/). All issues found by V12 will be judged as out of scope and ineligible for awards.

V12 findings will typically be posted in this section within the first two days of the competition.  

## Publicly known issues

_Anything included in this section is considered a publicly known issue and is therefore ineligible for awards._

## 🐺 C4: Begin Gist paste here (and delete this line)





# Scope

*See [scope.txt](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/scope.txt)*

### Files in scope


| File   | Logic Contracts | Interfaces | nSLOC | Purpose | Libraries used |
| ------ | --------------- | ---------- | ----- | -----   | ------------ |
| /crates/library/src/errors.rs | ****| **** | 31 | ||
| /crates/library/src/lib.rs | ****| **** | 4 | ||
| /crates/library/src/math/bn.rs | ****| **** | 488 | ||
| /crates/library/src/math/casting.rs | ****| **** | 34 | ||
| /crates/library/src/math/ceil_div.rs | ****| **** | 73 | ||
| /crates/library/src/math/floor_div.rs | ****| **** | 120 | ||
| /crates/library/src/math/merkle.rs | ****| **** | 386 | ||
| /crates/library/src/math/mod.rs | ****| **** | 8 | ||
| /crates/library/src/math/safe_math.rs | ****| **** | 129 | ||
| /crates/library/src/math/tick.rs | ****| **** | 204 | ||
| /crates/library/src/math/u256.rs | ****| **** | 494 | ||
| /crates/library/src/structs.rs | ****| **** | 16 | ||
| /crates/library/src/token/mod.rs | ****| **** | 2 | ||
| /crates/library/src/token/spl.rs | ****| **** | 130 | ||
| /programs/flashloan/src/constants.rs | ****| **** | 7 | ||
| /programs/flashloan/src/errors.rs | ****| **** | 32 | ||
| /programs/flashloan/src/events.rs | ****| **** | 13 | ||
| /programs/flashloan/src/invokes/liquidity_layer.rs | ****| **** | 91 | ||
| /programs/flashloan/src/invokes/mod.rs | ****| **** | 1 | ||
| /programs/flashloan/src/lib.rs | ****| **** | 138 | ||
| /programs/flashloan/src/state/context.rs | ****| **** | 99 | ||
| /programs/flashloan/src/state/mod.rs | ****| **** | 6 | ||
| /programs/flashloan/src/state/seeds.rs | ****| **** | 1 | ||
| /programs/flashloan/src/state/state.rs | ****| **** | 97 | ||
| /programs/flashloan/src/validate.rs | ****| **** | 118 | ||
| /programs/lending/src/constant.rs | ****| **** | 8 | ||
| /programs/lending/src/errors.rs | ****| **** | 30 | ||
| /programs/lending/src/events.rs | ****| **** | 42 | ||
| /programs/lending/src/invokes/f_token.rs | ****| **** | 47 | ||
| /programs/lending/src/invokes/liquidity_layer.rs | ****| **** | 91 | ||
| /programs/lending/src/invokes/mod.rs | ****| **** | 4 | ||
| /programs/lending/src/lib.rs | ****| **** | 95 | ||
| /programs/lending/src/module/admin.rs | ****| **** | 179 | ||
| /programs/lending/src/module/mod.rs | ****| **** | 4 | ||
| /programs/lending/src/module/user.rs | ****| **** | 63 | ||
| /programs/lending/src/state/context.rs | ****| **** | 370 | ||
| /programs/lending/src/state/mod.rs | ****| **** | 6 | ||
| /programs/lending/src/state/seeds.rs | ****| **** | 6 | ||
| /programs/lending/src/state/state.rs | ****| **** | 28 | ||
| /programs/lending/src/utils/deposit.rs | ****| **** | 100 | ||
| /programs/lending/src/utils/helpers.rs | ****| **** | 191 | ||
| /programs/lending/src/utils/mod.rs | ****| **** | 8 | ||
| /programs/lending/src/utils/rebalance.rs | ****| **** | 38 | ||
| /programs/lending/src/utils/withdraw.rs | ****| **** | 80 | ||
| /programs/lendingRewardRateModel/src/constants.rs | ****| **** | 8 | ||
| /programs/lendingRewardRateModel/src/errors.rs | ****| **** | 30 | ||
| /programs/lendingRewardRateModel/src/events.rs | ****| **** | 37 | ||
| /programs/lendingRewardRateModel/src/invokes/lending.rs | ****| **** | 46 | ||
| /programs/lendingRewardRateModel/src/invokes/mod.rs | ****| **** | 2 | ||
| /programs/lendingRewardRateModel/src/lib.rs | ****| **** | 137 | ||
| /programs/lendingRewardRateModel/src/state/context.rs | ****| **** | 264 | ||
| /programs/lendingRewardRateModel/src/state/mod.rs | ****| **** | 6 | ||
| /programs/lendingRewardRateModel/src/state/seeds.rs | ****| **** | 2 | ||
| /programs/lendingRewardRateModel/src/state/state.rs | ****| **** | 81 | ||
| /programs/liquidity/src/constants.rs | ****| **** | 18 | ||
| /programs/liquidity/src/errors.rs | ****| **** | 90 | ||
| /programs/liquidity/src/events.rs | ****| **** | 106 | ||
| /programs/liquidity/src/lib.rs | ****| **** | 184 | ||
| /programs/liquidity/src/module/admin.rs | ****| **** | 614 | ||
| /programs/liquidity/src/module/mod.rs | ****| **** | 2 | ||
| /programs/liquidity/src/module/user.rs | ****| **** | 249 | ||
| /programs/liquidity/src/state/context.rs | ****| **** | 379 | ||
| /programs/liquidity/src/state/mod.rs | ****| **** | 16 | ||
| /programs/liquidity/src/state/rate_model.rs | ****| **** | 508 | ||
| /programs/liquidity/src/state/seeds.rs | ****| **** | 7 | ||
| /programs/liquidity/src/state/state.rs | ****| **** | 71 | ||
| /programs/liquidity/src/state/structs.rs | ****| **** | 49 | ||
| /programs/liquidity/src/state/token_reserve.rs | ****| **** | 422 | ||
| /programs/liquidity/src/state/user_borrow_position.rs | ****| **** | 194 | ||
| /programs/liquidity/src/state/user_supply_position.rs | ****| **** | 180 | ||
| /programs/liquidity/src/utils/mod.rs | ****| **** | 2 | ||
| /programs/liquidity/src/utils/token.rs | ****| **** | 37 | ||
| /programs/oracle/src/constants.rs | ****| **** | 21 | ||
| /programs/oracle/src/errors.rs | ****| **** | 54 | ||
| /programs/oracle/src/events.rs | ****| **** | 15 | ||
| /programs/oracle/src/helper.rs | ****| **** | 164 | ||
| /programs/oracle/src/lib.rs | ****| **** | 180 | ||
| /programs/oracle/src/modules/chainlink.rs | ****| **** | 24 | ||
| /programs/oracle/src/modules/jup_lend.rs | ****| **** | 64 | ||
| /programs/oracle/src/modules/mod.rs | ****| **** | 14 | ||
| /programs/oracle/src/modules/msol_pool.rs | ****| **** | 38 | ||
| /programs/oracle/src/modules/pyth.rs | ****| **** | 67 | ||
| /programs/oracle/src/modules/redstone.rs | ****| **** | 38 | ||
| /programs/oracle/src/modules/single_pool.rs | ****| **** | 121 | ||
| /programs/oracle/src/modules/stake_pool.rs | ****| **** | 78 | ||
| /programs/oracle/src/state/context.rs | ****| **** | 54 | ||
| /programs/oracle/src/state/mod.rs | ****| **** | 9 | ||
| /programs/oracle/src/state/schema.rs | ****| **** | 183 | ||
| /programs/oracle/src/state/seeds.rs | ****| **** | 2 | ||
| /programs/oracle/src/state/state.rs | ****| **** | 18 | ||
| /programs/oracle/src/state/structs.rs | ****| **** | 58 | ||
| /programs/vaults/src/constants.rs | ****| **** | 29 | ||
| /programs/vaults/src/errors.rs | ****| **** | 152 | ||
| /programs/vaults/src/events.rs | ****| **** | 153 | ||
| /programs/vaults/src/invokes/liquidity_layer.rs | ****| **** | 102 | ||
| /programs/vaults/src/invokes/mint.rs | ****| **** | 61 | ||
| /programs/vaults/src/invokes/mod.rs | ****| **** | 6 | ||
| /programs/vaults/src/invokes/oracle.rs | ****| **** | 53 | ||
| /programs/vaults/src/lib.rs | ****| **** | 194 | ||
| /programs/vaults/src/module/admin.rs | ****| **** | 556 | ||
| /programs/vaults/src/module/mod.rs | ****| **** | 3 | ||
| /programs/vaults/src/module/user.rs | ****| **** | 938 | ||
| /programs/vaults/src/module/view.rs | ****| **** | 22 | ||
| /programs/vaults/src/state/branch.rs | ****| **** | 216 | ||
| /programs/vaults/src/state/context.rs | ****| **** | 693 | ||
| /programs/vaults/src/state/mod.rs | ****| **** | 22 | ||
| /programs/vaults/src/state/position.rs | ****| **** | 166 | ||
| /programs/vaults/src/state/seeds.rs | ****| **** | 11 | ||
| /programs/vaults/src/state/state.rs | ****| **** | 20 | ||
| /programs/vaults/src/state/structs.rs | ****| **** | 419 | ||
| /programs/vaults/src/state/tick.rs | ****| **** | 140 | ||
| /programs/vaults/src/state/tick_has_debt.rs | ****| **** | 1109 | ||
| /programs/vaults/src/state/tick_id_liquidation.rs | ****| **** | 78 | ||
| /programs/vaults/src/state/vault_config.rs | ****| **** | 22 | ||
| /programs/vaults/src/state/vault_state.rs | ****| **** | 313 | ||
| /programs/vaults/src/utils/common.rs | ****| **** | 52 | ||
| /programs/vaults/src/utils/liquidate.rs | ****| **** | 121 | ||
| /programs/vaults/src/utils/mod.rs | ****| **** | 8 | ||
| /programs/vaults/src/utils/operate.rs | ****| **** | 214 | ||
| /programs/vaults/src/utils/validate.rs | ****| **** | 267 | ||
| **Totals** | **** | **** | **15195** | | |

### Files out of scope

*See [out_of_scope.txt](https://github.com/code-423n4/2026-02-jupiter-lend/blob/main/out_of_scope.txt)*

| File         |
| ------------ |
| ./test-utils/rust/src/builder.rs |
| ./test-utils/rust/src/core/accounts.rs |
| ./test-utils/rust/src/core/mod.rs |
| ./test-utils/rust/src/core/state.rs |
| ./test-utils/rust/src/core/transactions.rs |
| ./test-utils/rust/src/core/vm.rs |
| ./test-utils/rust/src/errors.rs |
| ./test-utils/rust/src/fork/mod.rs |
| ./test-utils/rust/src/fork/provider.rs |
| ./test-utils/rust/src/helpers/assertions.rs |
| ./test-utils/rust/src/helpers/fixtures.rs |
| ./test-utils/rust/src/helpers/lookup_table.rs |
| ./test-utils/rust/src/helpers/mod.rs |
| ./test-utils/rust/src/helpers/tokens.rs |
| ./test-utils/rust/src/internal/compute.rs |
| ./test-utils/rust/src/internal/conversions.rs |
| ./test-utils/rust/src/internal/mod.rs |
| ./test-utils/rust/src/internal/rpc.rs |
| ./test-utils/rust/src/lib.rs |
| ./tests/src/addresses.rs |
| ./tests/src/connection.rs |
| ./tests/src/lending/fixture/mod.rs |
| ./tests/src/lending/fixture/setup.rs |
| ./tests/src/lending/mod.rs |
| ./tests/src/lib.rs |
| ./tests/src/liquidity/base_test.rs |
| ./tests/src/liquidity/borrow_limit_test.rs |
| ./tests/src/liquidity/borrow_test.rs |
| ./tests/src/liquidity/claim_test.rs |
| ./tests/src/liquidity/fixture/mod.rs |
| ./tests/src/liquidity/fixture/resolver.rs |
| ./tests/src/liquidity/fixture/setup.rs |
| ./tests/src/liquidity/liquidity_yield_test.rs |
| ./tests/src/liquidity/mod.rs |
| ./tests/src/liquidity/operate_test.rs |
| ./tests/src/liquidity/payback_test.rs |
| ./tests/src/liquidity/supply_test.rs |
| ./tests/src/liquidity/withdraw_test.rs |
| ./tests/src/liquidity/withdrawal_limit_test.rs |
| ./tests/src/oracle/chainlink.rs |
| ./tests/src/oracle/jup_lend.rs |
| ./tests/src/oracle/litesvm_mainnet_test.rs |
| ./tests/src/oracle/mod.rs |
| ./tests/src/oracle/msol.rs |
| ./tests/src/oracle/redstone.rs |
| ./tests/src/oracle/single_pool.rs |
| ./tests/src/oracle/stake_pool.rs |
| ./tests/src/utils/litesvm.rs |
| ./tests/src/utils/mod.rs |
| ./tests/src/vaults/base_test.rs |
| ./tests/src/vaults/combination_test.rs |
| ./tests/src/vaults/fixture/mod.rs |
| ./tests/src/vaults/fixture/resolver.rs |
| ./tests/src/vaults/fixture/setup.rs |
| ./tests/src/vaults/liquidate_test.rs |
| ./tests/src/vaults/mod.rs |
| Totals: 56 |

