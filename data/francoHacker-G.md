++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

  for (uint256 i = 0; i < length; /** NOTE: Removed i++ **/ ) {
                // Do the thing
                // Unchecked pre-increment is cheapest
                unchecked { ++i; }
        }    

instances:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199-L476

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38   