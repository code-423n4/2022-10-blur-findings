Throughout the Blur contracts, revert statements return with a `string` reason; which can be optimized to use much less gas over the course of the project using custom errors. For example, on [Line 139](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L139) of `BlurExchange.sol`, the following line seen below reverts with the string `Sell has invalid parameters`.
```
require(_validateOrderParameters(sell.order, sellHash), "Sell has invalid parameters");
```
This can be optimized by throwing a custom error which uses three less op-codes to do the same thing. The optimized equivalent can be seen below.
```
err SellInvalidParams();
...
if(!_validateOrderParameters(sell.order, sellHash)) revert SellInvalidParams();
```