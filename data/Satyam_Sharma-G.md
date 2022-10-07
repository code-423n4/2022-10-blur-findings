ISsue 1.
- change `i++` in your for loops to `unchecked{++i}` .on L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

prefix arithmetic is a bit cheaper than postfix arithmetic, but if you do it in a for loop, this small amount of gas can pile up and be a big waste.

also, in solidity 0.8.0+, every arithmetic operation is checked for overflow and underflow, which adds a lot of gas to a single operation. 
Since in your for loop you don't have the risk for overflow, you can surround the operation in unchecked{} to save lost of gas.

Issue 2.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L278

 function _canSettleOrder(uint256 listingTime, uint256 expirationTime)
        view
        internal
        returns (bool)
    {
        return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);
    }
unnecessary OR condition applied if expirationTime == 0 = true than condition after || will not get execute!! write only 
one condition either expirationTime == 0 or block.timestamp < expirationTime since both condion could not be true at same time. 
Simutaneously Waste of gas by applying two conditions. 

Issue 3.

Redundant return for named returns
Redundant code increase contract size and gas usage at deployment.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L416-L419
function _canMatchOrders(Order calldata sell, Order calldata buy)
        internal
        view
        returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType) 

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L433
return (price, tokenId, amount, assetType)

L433, return (price, tokenId, amount, assetType) is redundant

Issue 4. 

No need to initialize variables with default values

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475
 uint256 totalFee = 0;

If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). 
Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Remove explicit initialization for default values.

Issue5.

Use custom error to reduce the wastage of gas.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139-L143
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L424
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L428
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534

Issue 6.

No need to create a local variable and initialize already create variable in that it will be a waste of gas.


https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L63-L69
function viewWhitelistedPolicies(uint256 cursor, uint256 size)
        external
        view
        override
        returns (address[] memory, uint256)
    {
        uint256 length = size; //no need of declaring length 


Issue 7.

No need to initialize variables with default values

Impact
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). 
Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Instances include:
 for (uint8 i = 0; i < orders.length; i++)
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
 for (uint8 i = 0; i < fees.length; i++)
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
  for (uint256 i = 0; i < length; i++)
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
 for (uint256 i = 0; i < fees.length; i++)
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77
 for (uint256 i = 0; i < proof.length; i++)
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

Recommended Mitigation Steps
Remove explicit initialization for default values

Issue 8.
prefix arithmetic is a bit cheaper than postfix arithmetic, but if you do it in a for loop, this small amount of gas can pile up and be a big waste.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/
BlurExchange.sol#L199
    for (uint8 i = 0; i < orders.length; i++) 

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
    for (uint8 i = 0; i < fees.length; i++) 
