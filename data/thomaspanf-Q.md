# C4 blur gas optimization report thomaspanf

### [Gas Optimization] loop condition calculated multiple times

**File(s)**: [`BlurExchange.sol`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol)

**Description**: `orders.length` is calculated for each iteration of this for loop, causing additional gas usage. 

```
198    function cancelOrders(Order[] calldata orders) external {
199        for (uint8 i = 0; i < orders.length; i++) {
197            cancelOrder(orders[i]);
```

there is another instance of this on [L#476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476):
```
476     for (uint8 i = 0; i < fees.length; i++) {
```

**Recommendation(s)**: Consider computing the length of loop conditions outside of for loops. 

**POC**: 
```    
function cancelOrders(Order[] calldata orders) external {
        uint256 order_len = orders.length; 
        for (uint8 i = 0; i < order_len; i++) {
            cancelOrder(orders[i]);
```

---
### [Gas Optimization] use \++i instead of i++ 

**File(s)**: [`BlurExchange.sol`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol)[`Policymanager.sol`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol)

**Description**: There are three instances where `++i` should be used instead of `i++` for very minor gas savings

`BlurExchange.sol` [#L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199) , [#L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476):
```
199     for (uint8 i = 0; i < orders.length; i++) {
.
476     for (uint8 i = 0; i < fees.length; i++) {
```
'PolicyManager.sol' [#L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)
```
77      for (uint256 i = 0; i < length; i++) {
```


**Recommendation(s)**: Consider replacing `i++` with `++i`

---


### [Gas Optimization] No need to initialize variables to default values 

**File(s)**: [`BlurExchange.sol`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol)

**Description**: Explicitly initializing a variable to its default value costs additional gas. 

`BlurExchange.sol` [L#475](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475):

```
475     uint256 totalFee = 0;
```

**Recommendation(s)**: Consider replacing `uint256 totalFee = 0;` with `uint256 totalFee;`

