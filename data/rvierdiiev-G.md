1.	For the loops use `++i` instead of `i++`. Also cache array size into variable. Use uint256 instead of any other uint types. And do not initialize I value with 0.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199-L201
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
 https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38
2.	Use` uint256` value 1 and 2 instead of boolean to save gas.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10