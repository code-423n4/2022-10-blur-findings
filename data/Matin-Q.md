1 - Use of ```transfer()``` may lead to failure. Use ```call``` instead of solidity's ```transfer```:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L508

2 - Function _transferTo lacks reentrancy guard:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L496