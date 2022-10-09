## Low Risk

### Missing non-zero check

There is some address `_weth`, `_executionDelegate`, `_policyManager`, `_oracle` need to be check zero address at BlurExchange.initialize()

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113-L116

### There is no check with `sell.order.fee` when match orders.
```
        return (
            (makerAsk.side != takerBid.side) &&
            (makerAsk.paymentToken == takerBid.paymentToken) &&
            (makerAsk.collection == takerBid.collection) &&
            (makerAsk.tokenId == takerBid.tokenId) &&
            (makerAsk.matchingPolicy == takerBid.matchingPolicy) &&
            (makerAsk.price == takerBid.price),
            makerAsk.price,
            makerAsk.tokenId,
            1,
            AssetType.ERC721
        );
```

There is no check logic makerAsk.fee and tackerBid.fee are same.
Just use `sell.order.fee` when `_executeTokenTransfer()` this could be triky.

### There is no check with `executionDelegate.transferERC20` return value.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L511

`Executiondelegate.transferERC20()` has bool return value that transfer is success or not.
When transfer token at BlurExchange, this return value need to be checked.

### There is no chain id and contract address at hash order.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L84-L110

Nonce is in hash order but if blur is deploy at different chain, this signed hash order could be re-use.
So I recommand include chain id and contract address at hash message.

### DOMAIN_SEPARATOR are not immutable

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L37

DOMAIN_SEPARATOR are not immutable.
https://github.com/code-423n4/2021-11-malt-findings/issues/349



## Non Risk

### Constants should be written in UPPER_CASE

Constants should be written in UPPER_CASE, see Solidity naming conventions.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57-L58


### Doesn't emit Opened event when initialize BlurExchange.

When execute BlurExchange.initialize() `isOpen` is set `1`.

This mean Exchange is now open, so need to emit Opened event.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L104

### No revert message when execute() fail with `sell.order.side != Side.Sell`

There is no revert message at `require(sell.order.side == Side.Sell);`.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134

### Using extcodesize

When using solidity version >0.8.1 `extcodesize` can replace to `[address].code.length`

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L554-L556

