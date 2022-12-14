## [G-1] Cache the result of `sell.order.listingTime <= buy.order.listingTime`
Caching this result in BlurExchange.execute (https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L168) reduces the deploy gas usage by 6300. An example implementation would be:
```sol
bool sellFirst = sell.order.listingTime <= buy.order.listingTime;
emit OrdersMatched(
    sellFirst ? sell.order.trader : buy.order.trader,
    !sellFirst ? sell.order.trader : buy.order.trader,
    ...
);
```

## [G-2] Redundant `EnumerableSet.contains` call before adding/removing policies
`EnumerableSet.add` and `EnumerableSet.remove`, both internally call `contains` and return false if the operation failed based on `contain`'s return value. Removal of the two redundant checks in `PolicyManager` will result in decrease of 8200 gas at deployment of BlurExchange and about 260 when calling the involved external/public functions. Example implementation:
```sol
function addPolicy(address policy) external override onlyOwner {
    require(_whitelistedPolicies.add(policy), "Already whitelisted");
    ...

function removePolicy(address policy) external override onlyOwner {
    require(_whitelistedPolicies.remove(policy), "Not whitelisted");
    ...
```

## [G-3] Use `uint256` for `reentrancyLock`
Using a `bool` for `reentrancyLock` increases the gas costs, as compared to uint256. Changing the type will result in a decrease of 4000 gas at deployment of BlurExchange and 250 gas for every `execute` call. 
In general, it is not recommended to use smaller variables since they use more gas. More details can be found here: 
https://ethereum.stackexchange.com/questions/3067/why-does-uint8-cost-more-gas-than-uint256.

Example implementation:
```sol
uint reentrancyLock = 0;

/* Prevent a contract function from being reentrant-called. */
modifier reentrancyGuard {
    require(reentrancyLock == 0, "Reentrancy detected");
    reentrancyLock = 1;
    _;
    reentrancyLock = 0;
}
```

## [G-4] Loop variable increment can be done inside `unchecked` block
There are 5 instances across the codebase, where the loop is bounded to the length of an array. In these cases, the loop variable can not overflow, and can be safely incremented inside a `unchecked` block to save gas.