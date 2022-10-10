## C4 audit contest: 2022-10-blur

### Low Risk: Nonces not incremented after trade

Description: Nonces of buyer / seller is not incremented after the execution of the trade. 
This means users will need to call `incrementNonce()` themselves in between every trade to keep on trading 
with the same address. This could be intended behaviour. Seeing as `bulk signatures` are implemented
it seems multiple trades could occur without the user being online to increment its nonce. Making me believe
this behaviour is not intended.

Suggestion: Increment the nonces of the users at the end of `execute()`

Reference: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L128

### Non-Critical: Unused buyer fees

Description: The `execute()` function takes a buy and sell input. However, the `fees` field of the `buy`
input is (rightfully) unused. We could use two different inputs one without any `fees` and another one with `fees`
to avoid confusion and save gas by using less calldata.

Reference: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L150
