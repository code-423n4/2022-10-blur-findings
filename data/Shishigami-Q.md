# **Report QA**


## [QA-1] Unused internal variable

[BlurExchange.sol#388](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#388)

## [QA-2] Use of immutable instead of constant keyword when appropriate

`immutable` keyword is reserved for expressions while constant for litteral values.

1. EIP712.sol
  - [EIP712.sol#20](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#20)
  - [EIP712.sol#23](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#23)
  - [EIP712.sol#26](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#26)
  - [EIP712.sol#29](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#29)
  - [EIP712.sol#33](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#3)


## [QA-3] Public function not called by contract should be external

1. MerkleVerifier.sol
  - [MerkleVerifier.sol#21](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#21)


## [QA-4] Event is missing indexed fields

## [QA-5] Typo

[BlurExchange.sol#387](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#387) musted into must
