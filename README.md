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














# Foundry Cross Chain Rebase Token

This is a section of the Cyfrin Foundry Solidity course.

# About

This project is a cross-chain rebase token where users can depost ETH in exchange for rebase tokens which accrue rewards over time.

- [Foundry Cross Chain Rebase Token](#foundry-cross-chain-rebase-token)
- [About](#about)
- [Getting Started](#getting-started)
  - [Requirements](#requirements)
  - [Quickstart](#quickstart)
- [Updates](#updates)
- [Usage](#usage)
  - [Start a local node](#start-a-local-node)
  - [Deploy](#deploy)
  - [Deploy - Other Network](#deploy---other-network)
  - [Testing](#testing)
    - [Test Coverage](#test-coverage)
- [Deployment to a testnet or mainnet](#deployment-to-a-testnet-or-mainnet)
  - [Scripts](#scripts)
  - [Estimate gas](#estimate-gas)
- [Formatting](#formatting)
- [Thank you!](#thank-you)

# Getting Started

## Requirements

- [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
  - You'll know you did it right if you can run `git --version` and you see a response like `git version x.x.x`
- [foundry](https://getfoundry.sh/)
  - You'll know you did it right if you can run `forge --version` and you see a response like `forge 0.2.0 (816e00b 2023-03-16T00:05:26.396218Z)`

## Quickstart

```
git clone https://github.com/Cyfrin/foundry-cross-chain-rebase-token-cu
cd foundry-cross-chain-rebase-token-cu
forge build
```

# Updates

# Usage

## Start a local node

```
make anvil
```

## Deploy

This will default to your local node. You need to have it running in another terminal in order for it to deploy.

```
make deploy
```

## Deploy - Other Network

[See below](#deployment-to-a-testnet-or-mainnet)

## Testing

We talk about 4 test tiers on Updraft:

1. Unit
2. Integration
3. Forked
4. Staging

In this repo we cover #1 and Fuzzing.

```
forge test
```

### Testing Coverage

```
forge coverage
```

and for coverage based testing:

```
forge coverage --report debug
```

# Deployment to a testnet or mainnet

1. Setup environment variables

You'll want to set your `SEPOLIA_RPC_URL` and `PRIVATE_KEY` as environment variables. You can add them to a `.env` file, similar to what you see in `.env.example`.

- `PRIVATE_KEY`: The private key of your account (like from [metamask](https://metamask.io/)). **NOTE:** FOR DEVELOPMENT, PLEASE USE A KEY THAT DOESN'T HAVE ANY REAL FUNDS ASSOCIATED WITH IT.
  - You can [learn how to export it here](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key).
- `SEPOLIA_RPC_URL`: This is url of the sepolia testnet node you're working with. You can get setup with one for free from [Alchemy](https://alchemy.com/?a=673c802981)

Optionally, add your `ETHERSCAN_API_KEY` if you want to verify your contract on [Etherscan](https://etherscan.io/).

1. Get testnet ETH

Head over to [faucets.chain.link](https://faucets.chain.link/) and get some testnet ETH. You should see the ETH show up in your metamask.

2. Deploy

```
make deploy ARGS="--network sepolia"
```

## Scripts

Instead of scripts, we can directly use the `cast` command to interact with the contract.

For example, on Sepolia:

1. Get some RebaseTokens

```
cast send <vault-contract-address> "deposit()" --value 0.1ether --rpc-url $SEPOLIA_RPC_URL --wallet
```

2. Redeem RebaseTokens for ETH

```
cast send <vault-contractaddress> "redeem(uint256)" 10000000000000000 --rpc-url $SEPOLIA_RPC_URL --wallet
```

## Estimate gas

You can estimate how much gas things cost by running:

```
forge snapshot
```

And you'll see an output file called `.gas-snapshot`

# Formatting

To run code formatting:

```
forge fmt
```

# Thank you!

## Project design and assumptions

WHATEVER INTEREST THEY DEPOSIT WITH, THEY STICK WITH

This project is a cross-chain rebase token that integrates Chainlink CCIP to enable users to bridge their tokens cross-chain

### NOTES

- assumed rewards are in contract
- Protocol rewards early users and users which bridge to the L2
  - The interest rate decreases discretely
  - The interest rate when a user bridges is bridges with them and stays static. So, by bridging you get to keep your high interest rate.
- You can only deposit and withdraw on the L1.
- You cannot earn interest in the time while bridging.

Don't forget to bridge back the amount of interest they accrued on the destination chain in that time
