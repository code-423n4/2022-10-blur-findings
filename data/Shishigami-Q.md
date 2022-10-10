# **Report Low/non critical findings**

## [L-1] Issue in canceling multiple orders with `incrementNonce`

- Internal function incrementNonce [BlurExchange.sol#207-210](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L207-L210)
- External function Execute: [BlurExchange.sol#136-140](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L136-L140)

When cancelling all current orders for a user using function `incrementNonce`, storage variable `cancelledOrFilled` is not updated like it is when cancelling single or bulk orders with `cancelOrder` or `cancelOrders`.

While in the current contract the function `execute` computes the new hash before passing it to the function `_validateOrderParameters`, in later upgrades developpers should be aware of it.

It could result in `_validateOrderParameters` to return true if the `orderHash` is not computed just before every single time.

# **Report QA**

## [QA-1] Unused internal variable

[BlurExchange.sol#388](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388)

## [QA-2] Use of immutable instead of constant keyword when appropriate

`immutable` keyword is reserved for expressions while constant for litteral values.

1. EIP712.sol
  - [EIP712.sol#20](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L20)
  - [EIP712.sol#23](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23)
  - [EIP712.sol#26](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26)
  - [EIP712.sol#29](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29)
  - [EIP712.sol#33](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L3)


## [QA-3] Public function not called by contract should be external

1. MerkleVerifier.sol
  - [MerkleVerifier.sol#21](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L21)


## [QA-4] Event is missing indexed fields

## [QA-5] Typo

[BlurExchange.sol#387](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L387) musted into must
