
# Non Critical
## [N-1]`require()`/`revert()` statements should have descriptive reason strings
```solidity
contracts/BlurExchange.sol:134:        require(sell.order.side == Side.Sell);
contracts/BlurExchange.sol:183:        require(msg.sender == order.trader);
contracts/BlurExchange.sol:452:            require(msg.value == price);
```
