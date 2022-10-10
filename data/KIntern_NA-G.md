# [2022-10-blur] gas report 

###### tags: `c4`, `2022-09-blur`, `gas`

## `++I` COSTS LESS GAS THAN `I++`, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS 
Saves 5 gas per loop
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

## `++I`/ `I++` SHOULD BE UNCHECKED{`++I`}/ UNCHECKED{`I++`} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

## STRUCTS CAN BE PACKED INTO FEWER STORAGE SLOTS
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/OrderStructs.sol#L13-L28

## ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS REQUIRE() OR IF-STATEMENT
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L485

## ADD UNCHECKED {} FOR ADDITIONS WHERE THE OPERANDS CANNOT OVERFLOW IN REALITY
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208

## NOT NECESSARY TO USE NEW VARIABLES `_v, _r, _s`
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388-L389
Should replace by
```solidity=
(bytes32[] memory merklePath, v, r, s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```

## USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS `INVERSE_BASIS_POINT` TO SAVE GAS
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59

## INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L548
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L278-L284