## `execute()` can be more secure
`execute()` can be more secure by considering the check effect pattern. Actually this function is already safe from reentrancy attack, but we can improve it.
the way to improve it is by place the external calls function to the last line of the function.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L164-L165


```
        (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType) = _canMatchOrders(sell.order, buy.order);

        _executeFundsTransfer(
            sell.order.trader,
            buy.order.trader,
            sell.order.paymentToken,
            sell.order.fees,
            price
        );
        _executeTokenTransfer(
            sell.order.collection,
            sell.order.trader,
            buy.order.trader,
            tokenId,
            amount,
            assetType
        );

        /* Mark orders as filled. */
        cancelledOrFilled[sellHash] = true;
        cancelledOrFilled[buyHash] = true;

        emit OrdersMatched(
```

I recommend to change the code to :
```
        (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType) = _canMatchOrders(sell.order, buy.order);

        /* Mark orders as filled. */
        cancelledOrFilled[sellHash] = true;
        cancelledOrFilled[buyHash] = true;

        _executeFundsTransfer(
            sell.order.trader,
            buy.order.trader,
            sell.order.paymentToken,
            sell.order.fees,
            price
        );
        _executeTokenTransfer(
            sell.order.collection,
            sell.order.trader,
            buy.order.trader,
            tokenId,
            amount,
            assetType
        );

        emit OrdersMatched(
```