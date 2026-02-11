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

- If transaction loads more than 64 accounts, as per current architecture of protocol. Liquidation or operate can be DOS'd. (See zenith audit H1)
- Token extensions support can break protocol. (See zenith audit M5)
- No way to close the Position PDA to claim the rent back. (Rent claim related issues)
- Issues related to the first and last user of the vault, as we are aware of concerns around that and we create a forever intended locked dust position on each market.


✅ SCOUTS: Please format the response above 👆 so its not a wall of text and its readable.

# Overview

[ ⭐️ SPONSORS: add info here ]

## Links

- **Previous audits:**  https://dev.jup.ag/resources/audits#jupiter-lend
  - ✅ SCOUTS: If there are multiple report links, please format them in a list.
- **Documentation:** https://fluid.guides.instadapp.io
- **Website:** https://jup.ag/lend/earn
- **X/Twitter:** https://x.com/jup_lend

---

# Scope

[ ✅ SCOUTS: add scoping and technical details here ]

### Files in scope
- ✅ This should be completed using the `metrics.md` file
- ✅ Last row of the table should be Total: SLOC
- ✅ SCOUTS: Have the sponsor review and and confirm in text the details in the section titled "Scoping Q amp; A"

*For sponsors that don't use the scoping tool: list all files in scope in the table below (along with hyperlinks) -- and feel free to add notes to emphasize areas of focus.*

| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| [contracts/folder/sample.sol](https://github.com/code-423n4/repo-name/blob/contracts/folder/sample.sol) | 123 | This contract does XYZ | [`@openzeppelin/*`](https://openzeppelin.com/contracts/) |

### Files out of scope
✅ SCOUTS: List files/directories out of scope

# Additional context

## Areas of concern (where to focus for bugs)

Vaults Protocol: 
- After liquidation the final position of user should loaded correctly.
- Absorb and liquidation should work as intended. 
- Branch state should be maintained at places required, like closed and merged. 


✅ SCOUTS: Please format the response above 👆 so its not a wall of text and its readable.

## Main invariants

- Only owner of NFT should be able to withdraw or borrow from protocol. 
- Only whitelisted users on Liquidity layer should be able to interact with it.
- Amounts assigned to claim accounts on LL should always be available for claiming at any time (reserved)
- exchange prices should only ever increase
- no interactions on the extreme sides should be possible, where e.g. 1 is given as supply amount and leads to unexpected outcomes because of rounding, increasing on property in storage but not another
- the protocol (the contracts) must always be on the winning side. i.e. supply round down, borrow round up and so on
- withdraw and borrow limits must hold true


✅ SCOUTS: Please format the response above 👆 so its not a wall of text and its readable.

## All trusted roles in the protocol

All the child protocols of liquidity layer are permissionless. 
- Vaults
- Lending
- Flashloan 

Liquidity layer is whitelist based protocol.

✅ SCOUTS: Please format the response above 👆 using the template below👇

| Role                                | Description                       |
| --------------------------------------- | ---------------------------- |
| Owner                          | Has superpowers                |
| Administrator                             | Can change fees                       |

✅ SCOUTS: Please format the response above 👆 so its not a wall of text and its readable.

## Running tests

> git clone https://github.com/Instadapp/fluid-solana-programs.git
> cd fluid-solana-programs
> pnpm install
> pnpm build 

# Build a specific program
> pnpm build --program liquidity

# Build everything + run both suites (default behaviour)
> pnpm test

# TypeScript-only run for Vaults
> pnpm test --ts --program vaults --skip-build

# Rust suite with extra cargo flags
> pnpm test --rust -- -- --ignored

# List supported program names / aliases
> pnpm test --list-programs




✅ SCOUTS: Please format the response above 👆 using the template below👇

```bash
git clone https://github.com/code-423n4/2023-08-arbitrum
git submodule update --init --recursive
cd governance
foundryup
make install
make build
make sc-election-test
```
To run code coverage
```bash
make coverage
```

✅ SCOUTS: Add a screenshot of your terminal showing the test coverage

## Miscellaneous
Employees of Jupiter Lend and employees' family members are ineligible to participate in this audit.

Code4rena's rules cannot be overridden by the contents of this README. In case of doubt, please check with C4 staff.




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

