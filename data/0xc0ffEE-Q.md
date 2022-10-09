1. When order payment is native token, the spender is not restricted to buyer
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L451-L453
As anyone could call the function `execute` to execute the order. But when the payment token is native token, the ether value is spent by function caller
This could lead to unexpected scenarios such that: Alice buys NFT from Bob with price 1 ether, Chad buys NFT from Darwin with price 1 ether so Alice could trick Chad to execute Alice's order since the data when confirming from wallet is just hex data and an amount of ether value
Recommendations: Restrict `msg.sender == buyer` when payment token is native token

2. Should wrap to WETH if sending native token fails
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L508
When payment token is native token, the ether value is transferred to order seller and fee recipients specified in `sell.order.fees`. In case one of the fee recipients is a contract not receiving ether that the seller is not aware of this => the whole transaction is reverted
Recommendations: Consider wrap ETH to WETH and transfer to recipient if the transferring of ETH fails