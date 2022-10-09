1 - ```uint8``` is less efficient than ```uint256``` in loop iterations:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

2 - Caching array length in for loops can save gas:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

3 - Pre-increments are cheaper than post-increments: (use ```++i``` instead of ```i++```)

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

4 - Re-initialization of ```uint256``` and ```uint8``` to their default value is redundant:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

5 - Use ```x!=0``` instead of ```x>0``` as the x is unsigned integer:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L557

