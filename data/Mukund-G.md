## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
In for loop array length should not be looked up in every loop it cost more gas instead Caching the length save lot of gas. There are several loop like this in all contracts. 
`for (uint8 i = 0; i < fees.length; i++) {`
example :
`uint8 feesLength = fees.length ;
for( uint8 i ; i < feesLength;i++){`
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

## USE CUSTOM ERROR INSTEAD OF REVERT STRINGS TO SAVE GAS
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met).
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L36
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L140

## IT COSTS MORE GAS TO INITIALIZE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED
In for loop `uint8` is assigned to 0 but uint8 always initialize with zero assigning 0 cost more gas .
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

## USE ++I INSTEAD OF I++
in for loop using ++I insead of I++ save gas
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

## USE UNCHECKED BLOCK TO SAVE GAS
In Solidity 0.8 or higher the compiler will automatically take care of checking for overflows and underflows. So using `UNCHECKED{}` for variable which can not overflow/underflow it will save gas
for example you can use `UNCHECKED{++I}` in for loops or you can use it in some mathematic operation which can not be overflow/underflow. 