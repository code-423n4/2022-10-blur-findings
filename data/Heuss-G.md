https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back.

I advise following openzeppelin model to save some gas.

Openzeppelin model  and better explanation: 

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/8d908fe2c20503b05f888dd9f702e3fa6fa65840/contracts/security/ReentrancyGuard.sol#L23