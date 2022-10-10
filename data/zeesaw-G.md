### Don't Initialize Variables with Default Value

```
contracts/BlurExchange.sol::475 => uint256 totalFee = 0;
```
### Use != 0 instead of > 0 for Unsigned Integer Comparison

```
contracts/BlurExchange.sol::557 => return size > 0;
```
### Long Revert Strings

```
contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");
contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");
contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");
```
