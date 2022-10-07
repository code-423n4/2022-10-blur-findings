## 1. typo in comments 

musted be --> must be

- [BlurExchange.sol#L387](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L387)

settleable --> settable

- [BlurExchange.sol#L268](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L268)

initializating --> initialization of

- [ERC1967Proxy.sol#L19](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L19)


## 2. event is missing indexed fields
Each event should use three indexed fields if there are three or more fields

- [BlurExchange.sol#L79](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L79)


## 3. require()/revert() statements should have descriptive reason strings or custom error

- [BlurExchange.sol#L134](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134)
- [BlurExchange.sol#L183](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183)
- [BlurExchange.sol#L452](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L452)


## 4. constants should be defined rather than using magic numbers 
Even assembly can benefit from using readable constants instead of hex/numeric literals

- [BlurExchange.sol#L407](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407)


## 5. inconsistent use of named return variables

there is an inconsistent use of named return variables in the contract some functions return named variables, others return explicit values. consider adopting a consistent approach.
this would improve both the explicitness and readability of the code, and it may also help reduce regressions during future code refactors.

only these function returns differ from other contracts

- [BlurExchange.sol#L419](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L419)

- [ERC1967Proxy.sol#L29](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L29)


## 6. require() should be used instead of assert()

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert() do. assert() should be avoided even past solidity version 0.8.0 as its documentation states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

- [ERC1967Proxy.sol#L22](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L22)
