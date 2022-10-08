## Missing 0 address check

Value of `_weth` and `_oracle` should be checked against 0-address. Because, by accident, if those addresses are equal to 0-address, then 
that may break the protocol, and lead to re-deployment of the contract

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L113
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L116

## Require or revert statements should have descriptions 

A description on require or revert statement allow the viewer to understand the reason behind the revert. This increases the readability of the code.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183

## Dependence on block attributes like block.timestamp or block.number are risky, as they can be manipulated by the miners

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L283
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318

## Use of ecrecover is risky

`ecrecover` is vulnerable to signature malleability. Instead use ECDSA library
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L408

## Function doesn't follow check-effect-interact pattern

In function `_transferFees` , while sending payments , `totalFee` is updated after interacting with the external contract. This may lead to re-entrancy issues.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L477-L479

## All `onlyOwner` functions should be timelocked

Timelocked functions gives the users time to react, if the owner performs any malicious actions
 
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L215
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L224
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L233
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L242


## Use safetransferfrom instead of transferfrom

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L78
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L125