# QA REPORT

## [QA 00] Add Timelock for the following functions:


### Proof of concept:
- [Curation.sol#L98](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/curation/Curation.sol#L98)
- [DisputeManager.sol#L201](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/disputes/DisputeManager.sol#L201)
- [Staking.sol#L300](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L300)
- [RewardsManager.sol#L112](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/rewards/RewardsManager.sol#L112)
- [L2GraphToken.sol#L59](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/token/L2GraphToken.sol#L59)

## [QA 01] Two steps verification process is missing.
The process of transferring ownership is risky because typing the wrong address can have serious consequences.

### Proof of concept:
- [GraphProxy.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol)
- [GraphProxyStorage.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxyStorage.sol)
- [EthereumDIDRegistry.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/erc1056/EthereumDIDRegistry.sol)

## [QA 02] For solidity version less than 8, use safe math.


### Proof of concept:
- [Multicall.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/base/Multicall.sol)
- [GraphCurationToken.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/curation/GraphCurationToken.sol)
- [AllocationExchange.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/statechannels/AllocationExchange.sol)
- [L2ArbitrumMessenger.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/arbitrum/L2ArbitrumMessenger.sol)

## [QA 03] approve(0) should come before any approve of a general ERC20 token


### Proof of concept:
- [BridgeEscrow.sol#L28](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/BridgeEscrow.sol#L28)
- [GNS.sol#L173](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/GNS.sol#L173)
- [GRTWithdrawHelper.sol#L65](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/statechannels/GRTWithdrawHelper.sol#L65)

## [QA 04] Put nonReentrancy modifier at the beginning


### Proof of concept:
- [L2GraphTokenGateway.sol#L232](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/gateway/L2GraphTokenGateway.sol#L232)
