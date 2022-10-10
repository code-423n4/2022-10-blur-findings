Array.Length SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP and Increments can be unchecked
The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

( ++I/I++ ) SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77