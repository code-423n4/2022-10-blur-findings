## UNSAFE USE OF TRANSFER()/TRANSFERFROM() WITH IERC20
Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)‘s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20’s safeTransfer()/safeTransferFrom() instead
*There are 2 instances of this issue*
```solidity
contracts/BlurExchange.sol::508 => payable(to).transfer(amount);
contracts/ExecutionDelegate.sol::125 => return IERC20(token).transferFrom(from, to, amount);
```


## `REQUIRE()`/`REVERT()` STATEMENTS SHOULD HAVE DESCRIPTIVE REASON STRINGS

*There are 3 instances of this issue*
```solidity
contracts/BlurExchange.sol::134 => require(sell.order.side == Side.Sell);
contracts/BlurExchange.sol::183 => require(msg.sender == order.trader);
contracts/BlurExchange.sol::452 => require(msg.value == price);
```

## `PUBLIC` FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED `EXTERNAL` INSTEAD
*There is 1 instance of this issue*
```solidity
contracts/BlurExchange.sol::95-102 =>     function initialize(
        uint chainId,
        address _weth,
        IExecutionDelegate _executionDelegate,
        IPolicyManager _policyManager,
        address _oracle,
        uint _blockRange
    ) public initializer {
```


## MSG.VALUE VALIDATION ON PAYABLE FUNCTIONS WHEN USING ERC20
There should be a require(0 == msg.value) to ensure no Ether is being sent to the exchange when the currency used in an order is a ERC20 token.
*There is 1 instance of this issue*
```solidity
contracts/BlurExchange.sol::128-133 =>         function execute(Input calldata sell, Input calldata buy)
        external
        payable
        reentrancyGuard
        whenOpen
    {
```

## Zero receiveAmount on `_transferFees`
`_transferFees` allow 0 receiveAmount. The require statement only checks for totalFee <= price, in this case the totalFee can be equal to price, making [BlurExchangeL-485](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L485) receiveAmount = 0. 
*There is 1 instance of this issue*
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L469-L487
```solidity
    function _transferFees(
        Fee[] calldata fees,
        address paymentToken,
        address from,
        uint256 price
    ) internal returns (uint256) {
        uint256 totalFee = 0;
        for (uint8 i = 0; i < fees.length; i++) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
        }

        require(totalFee <= price, "Total amount of fees are more than the price");

        /* Amount that will be received by seller. */
        uint256 receiveAmount = price - totalFee;
        return (receiveAmount);
    }
```