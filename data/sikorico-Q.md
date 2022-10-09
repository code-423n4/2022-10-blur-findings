# QA REPORT

## [QA 00] Missing MIT licence for the following contracts


### Proof of concept:
- [GraphGovernanceStorage.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/GraphGovernanceStorage.sol)
- [SubgraphNFTDescriptor.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/SubgraphNFTDescriptor.sol)
- [EpochManagerStorage.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/epochs/EpochManagerStorage.sol)
- [GraphProxyAdmin.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxyAdmin.sol)
- [GraphUpgradeable.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphUpgradeable.sol)

## [QA 01] Use require instead assert in the following locations


### Proof of concept:
- [GraphProxy.sol#L46](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol#L46)
- [GraphProxy.sol#L47](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol#L47)
- [GraphProxy.sol#L50](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol#L50)

## [QA 02] Use SafeMath in the following contracts to avoid unexpected overflow/underflow


### Proof of concept:
- [GraphCurationToken.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/curation/GraphCurationToken.sol)
- [Managed.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/Managed.sol)
- [AddressAliasHelper.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/arbitrum/AddressAliasHelper.sol)
- [EthereumDIDRegistry.sol](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/erc1056/EthereumDIDRegistry.sol)

## [QA 03] Timelock is missing for the following functions


### Proof of concept:
- [Staking.sol#L754](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L754)
- [GraphProxy.sol#L104](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol#L104)
- [Staking.sol#L319](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L319)
- [GNS.sol#L206](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/GNS.sol#L206)
- [SubgraphNFT.sol#L45](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/SubgraphNFT.sol#L45)

## [QA 04] Some tokens needs approve 0 before any approve above 0
Consider using increase/decrease approve notation instead approve to deal with that.

### Proof of concept:
- [GNS.sol#L173](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/discovery/GNS.sol#L173)
- [BridgeEscrow.sol#L28](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/BridgeEscrow.sol#L28)
- [AllocationExchange.sol#L77](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/statechannels/AllocationExchange.sol#L77)

## [QA 05] Not emitted event for state changes


### Proof of concept:
- [Governed.sol#L31](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/Governed.sol#L31)
- [AllocationExchange.sol#L64](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/statechannels/AllocationExchange.sol#L64)
- [GraphTokenUpgradeable.sol#L150](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/token/GraphTokenUpgradeable.sol#L150)

## [QA 06] Missing event indexers


### Proof of concept:
- [L1GraphTokenGateway.sol#L62](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L62)
- [L2GraphToken.sol#L30](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/token/L2GraphToken.sol#L30)
- [L2GraphTokenGateway.sol#L60](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/l2/gateway/L2GraphTokenGateway.sol#L60)
- [Managed.sol#L33](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/Managed.sol#L33)
- [L1GraphTokenGateway.sol#L58](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L58)

## [QA 07] Remove the name from the following unused function parameters


### Proof of concept:
- [L2ArbitrumMessenger.sol#L42](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/arbitrum/L2ArbitrumMessenger.sol#L42)
