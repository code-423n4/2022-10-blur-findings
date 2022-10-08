-> X = X + Y IS CHEAPER THAN X += Y (same for X = X - Y IS CHEAPER THAN X -= Y)

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#:~:text=.sender%5D-,%2B%3D,-1%3B
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#:~:text=i%5D.recipient%2C%20fee)%3B-,totalFee%20%2B%3D%20fee%3B,-%7D


-> REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#:~:text=%22Contract%20is%20not%20approved%20to%20make%20transfers%22

-> ++i costs less gas compared to i++ or i += 1 (Also --i costs less gas compared to i--- or i -= 1)

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#:~:text=i%20%3C%20length%3B-,i%2B%2B),-%7B
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#:~:text=fees.length%3B-,i%2B%2B),-%7B
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#:~:text=orders.length%3B-,i%2B%2B),-%7B
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#:~:text=fees.length%3B-,i%2B%2B),-%7B
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#:~:text=proof.length%3B-,i%2B%2B),-%7B

-> USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#:~:text=(orderHash%2C%20blockNumber)%3B-,uint8,-v%3B%20bytes32
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#:~:text=bytes32%20digest%2C-,uint8,-v%2C

->USING BOOLS FOR STORAGE INCURS OVERHEAD

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#:~:text=%2C%20Ownable%20%7B-,mapping(address%20%3D%3E%20bool),-public%20contracts%3B
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#:~:text=)%20public%20contracts%3B-,mapping(address%20%3D%3E%20bool),-public%20revokedApproval%3B
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#:~:text=mapping(bytes32%20%3D%3E%20bool)



