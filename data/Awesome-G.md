 ## pre-increment ("++i") is cheaper
It costs less to use pre-increment ("++i") instead of post-increment( "i++" ) around 5 gas cheaper than post-increment
For example:
[Line 199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
[Line 476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)
[Line 38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)
[Line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)

## Remove Shorthand Addition/Subtraction Assignment
Instead of using the shorthand of addition/subtraction assignment operators ("+=", "-=")  it costs less to remove the shorthand (```  x += y  ``` same as ```      x = x+y  ```)
For example:
[Line 479](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479) could be refactored to
``` totalFee = totalFee + fee; ```


## using "> 0" costs more gas than "!= 0"
This change saves about 6 gas per instance. The optimization works until solidity version 0.8.13 where there is a regression in gas costs.

For example:
```        
return size > 0;
```

[Line 557](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L557)

 ## Use Custom Errors Instead Of Revert()/require() Strings to Save Gas
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hit by avoiding having to [allocate and store the revert string](blog.soliditylang.org). Not defining the strings also save deployment gas



For Example: 


```
Line 139:      require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
Line 140:      require(_validateOrderParameters(buy.order, buyHash), "Buy has invalid parameters");

Line 142:      require(_validateSignatures(sell, sellHash), "Sell failed authorization");
Line 143:      require(_validateSignatures(buy, buyHash), "Buy failed authorization");
```
[Line 139-143](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139-L143
),

```
Line 77:        require(revokedApproval[from] == false, "User has revoked approval");
```
[Line 77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77)


## Public Functions Not Called by the Contract Should Be Declared External Instead
Functions not internally called may have their visibility changed to external to save gas. 
For Example:

```
Line 95      function initialize(
Line 96          uint chainId,
Line 97          address _weth,
Line 98          IExecutionDelegate _executionDelegate,
Line 99          IPolicyManager _policyManager,
Line 100         address _oracle,
Line 101         uint _blockRange
Line 102:    ) public initializer {
```
[Line 102](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L102)


## Function Order Affects Gas Consumption
public/external function names and public member variable names can be optimized to save gas. See [this](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per sorted position shifted

## Limit Revert Strings Length To 32 Characters
Revert strings cost more gas to deploy if the string is larger than 32 bytes. It costs an extra 9,500 gas per string exceeding that 32-byte size upon deployment.

Revert strings exceeding 32 bytes include instances:
``` 
Line 22:        require(contracts[msg.sender], "Contract is not approved to make transfers");
```
[Line 22](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22)

```
Line 482:        require(totalFee <= price, "Total amount of fees are more than the price");
```
[Line 482](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482)

## Default Values
If  a variable is not initialized it will have a default value (0x0, false, 0, etc., depending on the data type)
so instead of assigning a variable to zero, you should omit it
For example:

```
Line 10:    bool reentrancyLock = false;
```
[Line 10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10) 
```
Line 199:         for (uint8 i = 0; i < orders.length; i++) {
```
[Line 199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199) 
```
Line 475:         uint256 totalFee = 0;
Line 476:         for (uint8 i = 0; i < fees.length; i++) {
```
[Line 475-476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475-L476) 

***Should be rewritten as such***

 ```
Line 10: bool reentrancyLock;
``` 
```
Line 199:         for (uint8 i; i < orders.length; i++) {
```
```
Line 475:         uint256 totalFee;
Line 476:         for (uint8 i; i < fees.length; i++) {
```
this is the same and saves gas because the initial value for uint type is zero and the initial value type for bool is ```false```


## Functions With Access Control Cheaper if Payable
A function with access control marked as payable will be cheaper for legitimate callers: the compiler removes checks for ```msg.value```, saving approximately ```20``` gas per function call.

For example:
 
[Line 244](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L244) 
[Line 36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L36) 
[Line 36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36) 
***Recommended alleviation step***:
Mark these functions as payable
