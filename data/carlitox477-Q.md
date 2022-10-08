# BlurExchange#execute: allows to emit OrderCancelled and OrdersMatched in the same transaction if ```paymentToken==0``` and seller is a smart contract

## Impact
May affect or not normal behaviour of oracles or bots which listen to ```OrdersMatched``` and ```OrderCanceled``` events

## POC
A DAO create a smart contract to handle their NFT. Someone decide to buy an NFT of this SC
1. The SC calls BlurExchange ```execute``` function
1. Eventually [this line](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L508) of ```_transferTo``` function is called, allowing the SC to do anything like calling function ```cancelOrder```, which will emit an [OrderCanceled event](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L190)
1. Given ther is no check after [_executeFundsTransfer](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L147) in ```execute``` function, eventually an [OrdersMatched](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L167) event is emited

```OrdersMatched``` and ```OrderCanceled``` has been emited for the same order.

## Tools used:
Manual review

## Mitigation steps:
Add the ```reentrancyGuard``` modifier to function [cancelOrder](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L181)

# [NON-CRITICAL] Expressions for constant values such as call to keccak256(), should use immutable rather than constant
While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

Change [EIP721](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20-L35) constant to ```immutable```

# [NON-CRITICAL] public functions not called by contract should be declared external
* [BlurExchange#initialize](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L94-L102)
* [MerkleVerifier#_verifyProof](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L17)

# [LOW] Lack of checks in BlurExchange #initialize function
All addresses sent by parameter do not check being different from zero.