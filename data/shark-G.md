## 1. Consider using custom errors instead of `require()` statements
Introduced in v0.8.4, Custom errors are more gas-efficient due to not needing to store a revert string.
Note: Must be v0.8.4 or higher.

For more info, visit https://blog.soliditylang.org/2021/04/21/custom-errors/

An example:
`BlurExchange.sol`: [Line 134](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134)

## 2. Unnecessary variable initialization
Variables if not initialized to anything will have a default value, e.g. zero, false etc.

Here are some examples:
`BlurExchange.sol`: [Line 199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
`BlurExchange.sol` [Line 475](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475)
`BlurExchange.sol`: [Line 476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
## 3. Public visibility costs more gas than external visibility
Public functions not called internally can be changed to external visibility.

For example:
`BlurExchange.sol`: [Line 95-118](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95-L118) On these lines, the function `initialize()` can be changed to external visibility.

## 4. Using  `!= 0` is more gas efficient compared to `> 0`
`!= 0 ` costs about 6 gas less compared to `> 0`.

Example:
`BlurExchange.sol`: [Line 557](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L557) could be refactored to `size != 0` instead.

## 5. `++i` costs less gas compared to `i++`
`++i` saves 5 gas compared to `i++`, This is because `i++`  increments a value and returns the old value, Doing so holds two numbers in memory. `++i` only ever uses one number in memory.

Here are some examples:
`BlurExchange.sol`: [Line 199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
`BlurExchange.sol`: [Line 476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
 
## 6. `+=` and `-=` are less efficient
`+=` and `-=` costs around 22 more gas, The amount of gas wasted can be very large when repeated multiple times in a loop.

For example:
`BlurExchange.sol`: [Line 479](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479) could be refactored to: `totalFee = totalFee + fee;`

`BlurExchange.sol`: [Line 208](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208) could be refactored to: `nonces[msg.sender] = nonces[msg.sender] + 1;`