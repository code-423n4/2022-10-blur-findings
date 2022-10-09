# QA REPORT

## [LOW] Add constraints to the following fee parameter values


Example: [Staking.sol#L406](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/staking/Staking.sol#L406)

## [NON CRITICAL] Use of magic numbers in the following locations


Example: [Multicall.sol#L23](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/base/Multicall.sol#L23)

## [NON CRITICAL] The following functions may not be payable


### Proof of concept:
- [GraphProxy.sol#L199](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/upgrades/GraphProxy.sol#L199)
- [L1GraphTokenGateway.sol#L269](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/gateway/L1GraphTokenGateway.sol#L269)

## [NON CRITICAL] Missing event for the following functions


### Proof of concept:
- [Governed.sol#L31](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/governance/Governed.sol#L31)
- [AllocationExchange.sol#L64](https://github.com/code-423n4/2022-10-thegraph/tree/main/contracts/statechannels/AllocationExchange.sol#L64)

--