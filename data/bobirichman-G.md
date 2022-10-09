# GAS REPORT

## [GAS] Mark as payable If has onlyOwner modifier
In order to save gas you can put a payable modifier for functions that are called by protocol owners.

### Proof of concept:
- [Curation.sol#L98](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/curation/Curation.sol#L98)
- [DisputeManager.sol#L266](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/disputes/DisputeManager.sol#L266)

## [GAS] Use assembly opcodes iszero in the following locations


### Proof of concept:
- [RewardsManager.sol#L292](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/rewards/RewardsManager.sol#L292)
- [BancorFormula.sol#L374](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/bancor/BancorFormula.sol#L374)

## [GAS] Use bytes32 in the following locations


Example: [SubgraphNFT.sol#L152](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/SubgraphNFT.sol#L152)

## [GAS] In the following revert statements consider using custom error instead a message


### Proof of concept:
- [Managed.sol#L48](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/Managed.sol#L48)
- [Staking.sol#L728](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L728)

## [GAS] Use abiEncodePacked()


### Proof of concept:
- [DisputeManager.sol#L183](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/disputes/DisputeManager.sol#L183)
- [GRTWithdrawHelper.sol#L47](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/statechannels/GRTWithdrawHelper.sol#L47)

## [GAS] Use > instead != to compare uint with 0


### Proof of concept:
- [Staking.sol#L1504](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L1504)
- [Staking.sol#L485](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L485)

## [GAS] Do not cache msg.sender since loading msg.sender is more efficient than a local variable


### Proof of concept:
- [Staking.sol#L1367](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L1367)
- [BancorFormula.sol#L342](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/bancor/BancorFormula.sol#L342)
