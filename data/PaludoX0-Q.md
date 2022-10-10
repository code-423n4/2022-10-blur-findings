https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L30
Contract inherits fron Ownableupgradble, in case of ownership changment the contracts check if address is == 0 but it doesn't implement
a two steps check avoiding mistakes on owner address insertion.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L508
transfer of native token is preferrable to be done through call function, since "transfer" limits gas to 2300, but since reentrancy guard is implemented , it is not necessary.
Use a sentence like (bool succes, ) = recipient.call{value: amount}("")

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L95
Initialize function doesn't check if variables weth and chainId are !=0. There's no fucntion which can change the value at a later stage
