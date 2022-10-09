# GAS REPORT

## [GAS] Use assembly opcodes iszero in the following locations


### Proof of concept:
- [BlurExchange.sol#L315](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L315)
- [BlurExchange.sol#L501](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L501)

## [GAS] Mark as payable If has onlyOwner modifier
In order to save gas you can put a payable modifier for functions that are called by protocol owners.

### Proof of concept:
- [BlurExchange.sol#L43](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L43)
- [PolicyManager.sol#L25](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L25)
