## Incrementing for loop in unchecked

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

To increment a for loop. It's best practice to use:

`unchecked { ++i; } `

At the end of the loop as it uses significantly less gas. However, this does assume that `i` cannot overflow, which is not the case as of now. I've submitted a "low" report that acknowledges that issue.