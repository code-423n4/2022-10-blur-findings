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
#2 USING  CALL() INSTEAD OF TRANSFER() ON AN ADDRESS PAYABLE CAN SAVE GAS 
The claimer smart contract does implement a payable fallback which uses more than 2300 gas unit.
The claimer smart contract implements a payable fallback function that needs less than 2300 gas units but is called through proxy, raising the call’s gas usage above 2300.
Additionally, using higher than 2300 gas might be mandatory for some multisig wallets.
Whenever the user either fails to implement the payable fallback function or cumulative gas cost of the function sequence invoked on a native token transfer exceeds 2300 gas consumption limit the native tokens sent end up undelivered and the corresponding user funds return functionality will fail each time.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L508 

# 3  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479
# 4  ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L45
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L61
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L91
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L132
 # 5 Use != 0 instead of > 0 at the above mentioned codes. The variable is uint, so it will not be below 0 so it can just check != 0.
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L557

