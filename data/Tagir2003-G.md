# GAS REPORT

## [GAS] Caching msg.sender is unnecessary


### Proof of concept:
- [BlurExchange.sol#L312](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L312)
- [BlurExchange.sol#L201](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L201)

## [GAS] Cache the following arrays size before the loop


### Proof of concept:
- [BlurExchange.sol#L475](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L475)
- [BlurExchange.sol#L198](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L198)

## [GAS] Consider using custom errors
You can utilize customized errors in require statements to save gas.

### Proof of concept:
- [ExecutionDelegate.sol#L107](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L107)
- [BlurExchange.sol#L138](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L138)
