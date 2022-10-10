       # 1) <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP and Increments can be unchecked
###  The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
++I COSTS LESS GAS THAN I++
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset
uint256 length = arr.length;
For example
for (uint i; i < length; ) {
// do stuff
unchecked { ++i; }
}
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
# 2  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208
# 3  ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L45
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L61
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L91
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L132


