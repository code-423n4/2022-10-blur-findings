1. Arithmetic operations, which can't underflow or overflow can be unchecked to save good amount of gas.

Duo to the require statement, which states that the "price" needs to be bigger or equal to the to the "totalfFee",
the aritmetic operation `uint256 receiveAmount = price - totalFee;` cannot underflow.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482-L485

Duo to the if statement, which states that the "length" needs to bigger, the arithmetic operation `length = _whitelistedPolicies.length() - cursor;` can be unchecked.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L72

2.  Cache storage values in memory to minimize the gas cost

The code can be optimized by minimizing the number of SLOADs, since SLOADs are more expensive than MLOADs.

consider catching the state variable `uint256 public isOpen`:
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L44
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L48