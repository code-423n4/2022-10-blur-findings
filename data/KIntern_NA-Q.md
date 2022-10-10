# [2022-10-blur] QA report 

###### tags: `c4`, `2022-09-blur`, `QA`

# Should check fees.length is in 'uint8' 
Since the for loop just use a `uint8 i` to iterate through the `fees` array, we should check if the length of array `fees` is in `uint8` or not, if not revert with a custom error.
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

# Support cancel more than 2^8 orders
Function `cancelOrders` will cancel all of the orders in array `orders`. Since function just uses a `uint8 i` to iterate through the `orders` arrays, it won't let user cancels more than 2^8 orders --> Bad user experience
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199

# Should check `msg.value == amount` or not, if not refund the remaining to sender
User can mistakenly send a wrong amount of eth. We should refund the remaining to user or revert if the `msg.value != amount`
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L508