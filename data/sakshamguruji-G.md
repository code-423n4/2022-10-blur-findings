## Gas Optimizations
#### USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having 
to allocate and store the revert string. Not defining the strings also save deployment gas.
Instances of the following issue: 
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L14
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L36
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L140
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L142
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L143
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L237
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L424
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L428
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L431
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L452


##  IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED
Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

Instances of this issue:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

#### Remediation 
Change to something like
`for(uint256 i; condition ; postincrement/preincrement)`


##  ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND  WHILE-LOOPS
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. 
This saves 30-40 gas per loop

Instances of the following issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

#### Remediation 
Use the `++i` in an unchecked block


## ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS (--I/I-- TOO)
Saves 5 gas per loop

Instances of the issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

#### Remediation
Consider using `++i` instead of `i++`


## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
Reading array length at each iteration of the loop consumes more gas than necessary.

In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas
 per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Here, consider storing the array’s length in a variable before the for-loop, and use this new variable instead:

Instances of this issue : 

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476


## Using storage instead of memory for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array 
to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the
new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declaring the variable with the 
memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables,
will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole 
struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function
that requires memory, or if the array/struct is being read from another memory array/struct

Instances of this issue: 

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L359
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L75-L79


## USING BOOLS FOR STORAGE INCURS OVERHEAD
// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and
// pointer aliasing, and it cannot be disabled.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) 
when changing from false to true, after having been true in the past

Instances of this issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10


## MULTIPLE ADDRESS/ID MAPPINGS CAN BE COMBINED INTO A SINGLE MAPPING OF AN ADDRESS/ID TO A STRUCT, WHERE APPROPRIATE
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per
mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in 
the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having
to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

Instances of this issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L18-L19


## REDUCE THE SIZE OF ERROR MESSAGES (LONG REVERT STRINGS)
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert
 condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for 
computing memory offset, etc.

Instances of this issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482


##  USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
If needed, the value can be read from the verified contract source code. Saves 3406-3606 gas in deployment gas due to the
compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value
outside of where it’s used, and not adding another entry to the method ID table

Instances of this issue:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59


## STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE
Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) 
and each access thereafter (Gwarmacces - 100 gas) with a PUSH32 (3 gas).

Instances of this issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L63

This variable's value (weth) is initialized in the initialize function(constructor) and not set anywhere else.


## X += Y costs 22 more gas than X = X + Y.
This can mean a lot of gas wasted in a function call when the computation is
    repeated n times (loops)
    Recommended Mitigation
use X = X + Y instead of X += Y (same with -).

Instances of this issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479


##  AVOID EMITTING A STORAGE VARIABLE WHEN A MEMORY VALUE IS AVAILABLE
When they are the same, consider emitting the memory value instead of the storage value:

Instances of this issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L221  `use _executionDelegate instead`
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L230 `use _policyManager instead`
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L239 `use _oracle instead`
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L247 `use _blockRange instead`

## USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT 

This change saves 6 gas per instance. The optimization works until solidity version 0.8.13 where there is a regression in gas costs.

Instances of this issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L557


## STRUCTS CAN BE PACKED INTO FEWER STORAGE SLOTS
Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

There is 1 instance of this issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/OrderStructs.sol#L8-L11

Here the variable `rate` can be converted to a uint8 to save one storage slot.


## USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Instances of the following:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L347
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L383
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L385
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L403


## INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Instances of this issue:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L278
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L344
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L375
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L416
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L444
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L469
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L525
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L548









