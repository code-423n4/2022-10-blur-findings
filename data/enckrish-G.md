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

## [G-2] Use `uint256` for `reentrancyLock`
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