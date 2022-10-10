## USE CALL() INSTEAD OF TRANSFER()
`TRANSFER()`  was considered the go-to solution for making payments to EOAs or other smart contracts. But since the amount of gas they forward to the called smart contract is very low, the called smart contract can easily run out of gas. This would make the payment impossible.

The problem here is that even if the receiving contract is designed carefully not to exceed the gas limit, future changes in the gas costs for some opcodes can break this design.

Therefore, the recommended way of sending payments is the function call().
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L508

## ANYONE CAN CALL `incrementNonce()`.
# Impact
In `BlurExchange.sol`  `incrementNonce()` can be called by anyone.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L205
# Proof of Concept
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L205
on line 205 there is a comment which says ` @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order` but there is no check in this function if the user is trader or not.