# QA REPORT

## [LOW] Missing nonReentrancy modifier
The following functions allows attackers to try reentrancy since they are calling to external contracts / transferring eth. Consider adding a nonReentrancy modifier.

### Proof of concept:
- [ExecutionDelegate.sol#L76](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L76)
- [PolicyManager.sol#L36](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L36)

## [LOW] Missing pause functionality


### Proof of concept:
- [ExecutionDelegate.sol](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol)
- [BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol)

## [LOW] Not verified input
At the following functions you should verify the parameters that are being assigned to a state variable.

### Proof of concept:
- [BlurExchange.sol#L236](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L236)
- [BlurExchange.sol#L102](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L102)

## [LOW] The project is compiled with different solidity versions


## [LOW] Use safeTransfer
Use safeTransfer in the following locations

### Proof of concept:
- [ExecutionDelegate.sol#L77](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L77)
- [BlurExchange.sol#L539](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L539)

## [NON CRITICAL] Consider emitting an event at the following functions


Example: [BlurExchange.sol#L102](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L102)

## [NON CRITICAL] Missing function spec comments


### Proof of concept:
- [BlurExchange.sol#L227](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L227)
- [BlurExchange.sol#L53](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L53)

## [NON CRITICAL] The following events are not indexed


### Proof of concept:
- [BlurExchange.sol#L90](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L90)
- [BlurExchange.sol#L88](https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L88)

-