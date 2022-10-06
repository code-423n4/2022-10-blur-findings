## Impact
fees is called multiple times inside the function function, and memory variables should be used to see less gas
## Proof of Concept
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L477-L479
```
           uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
```


## Tools Used
vscode
## Recommended Mitigation Steps
function use Memory arrays