Table Of Content
================

* [GAS REPORT](#gas-report)
        * [Split require statement with & operator](#split-require-statement-with--operator)
        * [If the function is onlyOwner you may make it payable to reduce gas usage.](#if-the-function-is-onlyowner-you-may-make-it-payable-to-reduce-gas-usage)
        * [Using abiEncodePacked() is more efficient that abiEncode()](#using-abiencodepacked-is-more-efficient-that-abiencode)
        * [The following assignments are not in use](#the-following-assignments-are-not-in-use)
        * [Use bytes32 instead string whenever possible](#use-bytes32-instead-string-whenever-possible)
        * [Use custom errors](#use-custom-errors)

# GAS REPORT

## Split require statement with & operator
instead of require(A & B, ...) consider having two require statements require(A, ...) and require(B, ...) for better code quality and improved gas usage.

### Code Instances:
- [Staking.sol#L368](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L368)
- [DisputeManager.sol#L276](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/disputes/DisputeManager.sol#L276)
- [Governed.sol#L53](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/Governed.sol#L53)
- [BancorFormula.sol#L365](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/bancor/BancorFormula.sol#L365)

## If the function is onlyOwner you may make it payable to reduce gas usage.


### Code Instances:
- [L2GraphTokenGateway.sol#L117](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/gateway/L2GraphTokenGateway.sol#L117)
- [EpochManager.sol#L46](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/epochs/EpochManager.sol#L46)
- [Curation.sol#L128](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/curation/Curation.sol#L128)
- [GNS.sol#L206](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/GNS.sol#L206)

## Using abiEncodePacked() is more efficient that abiEncode()


### Code Instances:
- [L2GraphTokenGateway.sol#L268](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/gateway/L2GraphTokenGateway.sol#L268)
- [GraphTokenUpgradeable.sol#L160](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/token/GraphTokenUpgradeable.sol#L160)
- [GraphToken.sol#L104](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/token/GraphToken.sol#L104)
- [DisputeManager.sol#L183](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/disputes/DisputeManager.sol#L183)

## The following assignments are not in use


For instance, [BancorFormula.sol#L520](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/bancor/BancorFormula.sol#L520)

## Use bytes32 instead string whenever possible


For instance, [SubgraphNFT.sol#L152](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/SubgraphNFT.sol#L152)

## Use custom errors
In the following require statements you can use custom errors to save gas and improve code quality.

### Code Instances:
- [EpochManager.sol#L94](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/epochs/EpochManager.sol#L94)
- [GNS.sol#L308](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/GNS.sol#L308)
- [L1GraphTokenGateway.sol#L270](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L270)
- [Staking.sol#L973](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L973)
