## [G-1] Use custom error rather than require() with string explanation.

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

***File : PolicyManager.sol***

PolicyManager.sol#L26
PolicyManager.sol#L37

***File : ReentrancyGuarded.sol***

ReentrancyGuarded.sol#L14

***File : BlurExchange.sol***

BlurExchange.sol#L36
BlurExchange.sol#L139
BlurExchange.sol#L140
BlurExchange.sol#L142
BlurExchange.sol#L143
BlurExchange.sol#L219
BlurExchange.sol#L228
BlurExchange.sol#L237
BlurExchange.sol#L318
BlurExchange.sol#L407
BlurExchange.sol#L424
BlurExchange.sol#L428
BlurExchange.sol#L431
BlurExchange.sol#L482
BlurExchange.sol#L534

***File : ExecutionDelegate.sol***

ExecutionDelegate.sol#L22
ExecutionDelegate.sol#L77
ExecutionDelegate.sol#L92
ExecutionDelegate.sol#L108
ExecutionDelegate.sol#L124




## [G-2] Strings longer than 32bytes inside require()/revert cost extra gas.

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

***File : BlurExchange.sol***

BlurExchange.sol#L482

***File : ExecutionDelegate.sol***

ExecutionDelegate.sol#L22




## [G-3] Expressions for constant values such as a call to KECCAK256() should use immutable rather than constant.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

***File : EIP712.sol***

EIP712.sol#L20
EIP712.sol#L23
EIP712.sol#L26
EIP712.sol#L29
EIP712.sol#L33




## [G-4] Pre-increment (++i) costs less gas than Post-increment (i++), especially in for loops.

Pre-increment saves 5 gas per loop.

***File : PolicyManager.sol***

PolicyManager.sol#L77

***File : MerkleVerifier.sol***

MerkleVerifier.sol#L38

***File : EIP712.sol***

EIP712.sol#L77

***File : BlurExchange.sol***

BlurExchange.sol#L199
BlurExchange.sol#L476




## [G-5] It costs more gas to initialize variables to zero than to let the default of zero be applied.

if a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

***File : PolicyManager.sol***

PolicyManager.sol#L77

***File : MerkleVerifier.sol***

MerkleVerifier.sol#L38

***File : EIP712.sol***

EIP712.sol#L77

***File : BlurExchange.sol***

BlurExchange.sol#L199
BlurExchange.sol#L475
BlurExchange.sol#L476




## [G-6] ++i/i++ should be UNCHECKED{++i}/UNCHECKED{i++} when it is not possible for them to overflow (eg. in for and while loops).

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves around 40 gas per loop.

***File : PolicyManager.sol***

PolicyManager.sol#L77

***File : MerkleVerifier.sol***

MerkleVerifier.sol#L38

***File : EIP712.sol***

EIP712.sol#L77

***File : BlurExchange.sol***

BlurExchange.sol#L199
BlurExchange.sol#L476




## [G-7] Require() should be used instead of assert() to save gas left during a transaction.

using require instead of assert would allow to refund the remaining gas in case of error.

***File : ERC1967Proxy.sol***

ERC1967Proxy.sol#L22




## [G-8] Non-strict inequalities are cheaper than strict ones.

Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas). This also holds true between <= and <.

***File : PolicyManager.sol***

PolicyManager.sol#L71
PolicyManager.sol#L77

***File : MerkleVerifier.sol***

MerkleVerifier.sol#L38
MerkleVerifier.sol#L46

***File : EIP712.sol***

EIP712.sol#L77

***File : BlurExchange.sol***

BlurExchange.sol#L71
BlurExchange.sol#L72
BlurExchange.sol#L168
BlurExchange.sol#L169
BlurExchange.sol#L199
BlurExchange.sol#L283
BlurExchange.sol#L283
BlurExchange.sol#L318
BlurExchange.sol#L422
BlurExchange.sol#L437
BlurExchange.sol#L476
BlurExchange.sol#L482
BlurExchange.sol#L557

***File : ExecutionDelegate.sol***

ExecutionDelegate.sol#L18
ExecutionDelegate.sol#L19




## [G-9] Functions guaranteed to revert when called by normal users (eg. onlyOwner) can be marked payable.

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

***File : PolicyManager.sol***

PolicyManager.sol#L36
PolicyManager.sol#L25

***File : BlurExchange.sol***

BlurExchange.sol#L47
BlurExchange.sol#L43
BlurExchange.sol#L53

***File : ExecutionDelegate.sol***

ExecutionDelegate.sol#L36
ExecutionDelegate.sol#L45




## [G-10] Duplicated require()/revert() checks should be refactored to a modifier to save gas.

When a require statement is used multiple times, it is cheaper in deployment costs to use a modifier instead.

***File : ExecutionDelegate.sol***

require(revokedapproval[from]==false,"userhasrevokedapproval");
ExecutionDelegate.sol#L77
ExecutionDelegate.sol#L92
ExecutionDelegate.sol#L108
ExecutionDelegate.sol#L124




## [G-11] Using PRIVATE rather than PUBLIC for CONSTANTS saves GAS.

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.

***File : EIP712.sol***

EIP712.sol#L20
EIP712.sol#L23
EIP712.sol#L26
EIP712.sol#L29

***File : BlurExchange.sol***

BlurExchange.sol#L57
BlurExchange.sol#L58
BlurExchange.sol#L59




## [G-12] Don't compare BOOLEAN expressions to BOOLEAN literals?

if(<x> == true) should be restated as if(<x>). Similarly, if(<x> == false) should be restated as if(!<x>).

***File : BlurExchange.sol***

BlurExchange.sol#L267

***File : ExecutionDelegate.sol***

ExecutionDelegate.sol#L77
ExecutionDelegate.sol#L92
ExecutionDelegate.sol#L108
ExecutionDelegate.sol#L124




## [G-13] multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

***File : ExecutionDelegate.sol***

ExecutionDelegate.sol#L18
ExecutionDelegate.sol#L19




## [G-14] Use a more recent version of solidity.

Use a solidity version of at least 0.8.15 to correct bugs reported in previous version. One of these bugs resulted in a .push() operation on a bytes array in storage appending bytes that are not properly zero-initialized. This change significantly decreases the bytecode size (which impacts the deployment cost) 

***File : ERC1967Proxy.sol***

ERC1967Proxy.sol#L2
ERC1967Proxy.sol#L3



## [G-15] Add require/revert statement on function that change variables to revert if new value = old value.

Some of the functions implemented allow admin to change value of state variables. However, these functions do not check whether or not the change requested equals the current state. Adding require/revert statement at the beginning of the function allows to limit gas expenses in case current state equals the new requested state.

***File : ExecutionDelegate.sol***

ExecutionDelegate.sol#L37
ExecutionDelegate.sol#L47
ExecutionDelegate.sol#L57 

***File : BlurExchange.sol***

BlurExchange.sol#43
BlurExchange.sol#47


## [G-16] Consider reverting at the beginning of a function to avoid unnecessary gas costs.

***File : BlurExchange.sol***

BlurExchange.sol#513