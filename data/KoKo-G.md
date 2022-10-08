# <X < Y + 1> is cheaper than <X <= Y>

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L168 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482

# USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT IN A REQUIRE() STATEMENT

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L557

# INLINE FUNCTION WHERE POSSIBLE

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L45 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L49

# CONSTANTS SHOULD BE MARKED AS `IMMUTABLE` OR `CONSTANT`

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L37

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L16

# FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L25 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L36

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L47 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L181

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L45

# ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

# USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE DEPLOYMENT GAS

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L14

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L36 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L142 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L143 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L219 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L424 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L513 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534

# IT COSTS MORE GAS TO INITIALIZE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77

# DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L267

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124

# USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58

# USE CALLDATA FOR FUNCTION PARAMETERS

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L39

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L20 \
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L35

# STRING LONGER THAN 32 BYTES INCUR OVERHEAD

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482

# STATE VARIABLES SHOULD BE CACHED IN STACK VARIABLES RATHER THAN RE-READING THEM FROM STORAGE

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L71-L78
