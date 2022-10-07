Issue 1.
Zero address check are missing

Zero-address checks are a best-practise for input validation of 
critical address parameters. While the codebase applies this to 
most addresses in setters.

In function initializer parameters address _weth and address _oracle .... Also in variables 
address public weth(L63)  and address public oracle(L66)  which can initialize zero address due to 
which function initializer internal operations got affected on L113 and L116.
weth = _weth and blockRange = _blockRange 

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L63
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L66 
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95-L118
and https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L346
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L445
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L446

Issue 2.
Unclear if statement in function _transferTo
  
  if (amount == 0) {
        return;
     }

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L502
