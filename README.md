# Foundry Cross-Chain Rebase Token  
*A Cyfrin Updraft / Patrick Collins course project*  
Author: **Jason King**

---

## Overview
This repo contains a cross-chain rebase token that lets users deposit ETH on L1, earn rebasing rewards, and bridge their token (with its fixed interest rate) to an L2 via **Chainlink CCIP**.  
All contracts and tests are written with **Foundry**.

---


---

## Fixes & Gotchas (how we finally made it compile)

| What broke | The fix | Why |
|------------|---------|-----|
| CCIP `Internal.sol`, `Client.sol` couldn’t be found | Remap `@chainlink/contracts-ccip/contracts/libraries/` → `lib/chainlink-local/lib/chainlink-ccip/chains/evm/contracts/libraries/` | Those two libraries exist **only** in the Chainlink-Local repo |
| Vendor copy of `IERC20.sol` not found | Remap `@chainlink/contracts-ccip/src/v0.8/vendor/openzeppelin-solidity/v4.8.3/contracts/` → `lib/chainlink-local/lib/chainlink-evm/contracts/src/v0.8/vendor/openzeppelin-solidity/v4.8.3/contracts/` | CCIP ships its own OpenZeppelin 4.8.3 fork |
| Other CCIP imports still missing | Remap `@chainlink/contracts-ccip/src/v0.8/ccip/` **and** `@ccip/contracts/src/v0.8/ccip/` → `lib/chainlink-local/lib/chainlink-ccip/chains/evm/contracts/` | Covers everything else inside CCIP |


<details>
<summary>📄 Final working <code>foundry.toml</code></summary>

</details>

---

## Test Results

```solidity
forge test 
[⠒] Compiling...
No files changed, compilation skipped

Ran 12 tests for test/RebaseToken.t.sol:RebaseTokenTest
[PASS] testCannotCallBurn() (gas: 17015)
[PASS] testCannotCallMint() (gas: 20558)
[PASS] testCannotSetInterestRate(uint256) (runs: 256, μ: 14520, ~: 14520)
[PASS] testCannotWithdrawMoreThanBalance() (gas: 135924)
[PASS] testDeposit(uint256) (runs: 256, μ: 128420, ~: 127843)
[PASS] testDepositLinear(uint256) (runs: 256, μ: 150279, ~: 150690)
[PASS] testGetPrincipleAmount() (gas: 130448)
[PASS] testInterestRateCanOnlyDecrease(uint256) (runs: 256, μ: 23983, ~: 24215)
[PASS] testRedeemAfterTimeHasPassed(uint256,uint256) (runs: 256, μ: 153973, ~: 153621)
[PASS] testRedeemStraightAway(uint256) (runs: 256, μ: 139237, ~: 139550)
[PASS] testSetInterestRate(uint256) (runs: 256, μ: 148269, ~: 149136)
[PASS] testTransfer(uint256,uint256) (runs: 256, μ: 251819, ~: 252457)
Suite result: ok. 12 passed; 0 failed; 0 skipped; finished in 53.10ms (151.99ms CPU time)

Ran 3 tests for test/CrossChain.t.sol:CrossChainTest
[PASS] testBridgeAllTokens() (gas: 1429647)
[PASS] testBridgeAllTokensBack() (gas: 2217550)
[PASS] testBridgeTwice() (gas: 2599487)
Suite result: ok. 3 passed; 0 failed; 0 skipped; finished in 36.99s (68.73s CPU time)

Ran 2 test suites in 37.00s (37.04s CPU time): 15 tests passed, 0 failed, 0 skipped (15 total tests)
```


---



