Increments can be unchecked

In Solidity 0.8, there is a default overflow check on unsigned integers. It is possible to uncheck the increments in for loops. By doing this, we can save gas, but at the same time lose code readability.

Instances include:
BlurExchange.cancelOrders(): for (uint8 i = 0; i < orders.length; i++)
BlurExchange._transferFees(): for (uint8 i = 0; i < fees.length; i++)
PolicyManager.viewWhitelistedPolicies(): for (uint256 i = 0; i < length; i++)

The code would go from:

for (uint256 i; i < numIterations; i++) {  
 // ...  
}  

to

for (uint256 i; i < numIterations;) {  
 // ...  
 unchecked { i++; }  
}  