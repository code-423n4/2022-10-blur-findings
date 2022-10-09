# Low effect on readability
## [G-01] Optimisation for loop
The easiest thing to do is to optimize every `for` loop, the objective is to replace those like the following example:

    for  (uint8 i =  0; i < array.length; i++)  {
    	//do something
    }

to

    uint256 len =  array.length;
    for  (uint256 i; i < len;)  {
    	//do something
    	unchecked{ ++i; }
    }

By doing so, the `length` is cached which is cheaper than looking at it every loop, `i = 0` is not initialized since `uint` have already a default value of `0` , the increment is transformed to a cheaper form since it can't overflow. (This is cheaper because without `unchecked` there is a check for overflow at each calculation and with the post-increment, the EVM need to store the value with and without increment, but the pre-increment only store the value with increment.) and uint256 is cheaper than uint8 because the EVM don't need to transform it to work with it (the EVM works with 256 bits).

3 instances:

 - https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199-L201
 - https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476-L480
 - https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77-L79

Consider optimizing `for` loop.

With those change, these evolutions in gas average report can be observed:

    BlurExchange: cancelOrders: 71598 -> 71409 (-189)
    BlurExchange: execute: 272762 -> 272682 (-80)
    Deployment: PolicyManager: 582410 -> 574850 (-7560)
    Deployment: TestBlurExchange: 3208152 -> 3196313 (-11839)

## [G-02] Unnecessary public constant

Declaring a private constant is cheaper than a public one. In some case, a constant can be declared as private to save gas. It is the case if the constant don't need to be called outside the contract. A user could still read the value directly in the code instead of calling it, if needed.

3 instances:

 - https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57-L59

Consider changing those constants to private. (The code still pass all the test with these changes.)

With those change, these evolutions in gas average report can be observed:

    BlurExchange: close: 29341 -> 29319 (-22)
    BlurExchange: execute: 272762 -> 272717 (-45)
    BlurExchange: incrementNonce: 41379 -> 41357 (-22)
    BlurExchange: setBlockRange: 31841 -> 31819 (-22)
    BlurExchange: setOracle: 32190 -> 32255 (+65)
    Deployment: TestBlurExchange: 3208152 -> 3163046 (-45106)

## [G-03] Some variable can be cached

2 instances:

 - https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L128

In the function `execute`, `sell.order` and `buy.order` can be cached to save gas.

Consider adding:

    Order calldata sellOrder = sell.order;
    Order calldata buyOrder = buy.order;

at the start of the function and then replacing every instance of `sell.order` and `buy.order` by there cached version.

With those change, these evolutions in gas average report can be observed:

    BlurExchange: execute: 272762 -> 270627 (-2135)
    TestBlurExchange: 3208152 ->  3163214 (-44938)
