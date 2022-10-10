# I++ should be changed to UNCHECKED{++I} 
The **unchecked** keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This save 30-40 gas per loop.

Also ++I saves five gas per loop compared to I++

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.solL#199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.solL#476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.solL#77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.solL#77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.solL#38
 