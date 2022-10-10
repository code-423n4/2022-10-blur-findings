## 2022 10 Blur QA

### ./contracts/BlurExchange.sol

I don't feel re-entrancy will be an issue here as if the attack were possible it will drain eth/weth to fees and not benefit the attacker. 
For best practices though, consider swapping lines 478 & 479

[BlurExchange.sol#L476-L479](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476-L479)
From:
```solidity
    for (uint8 i = 0; i < fees.length; i++) { 
        uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
        _transferTo(paymentToken, from, fees[i].recipient, fee);        
        totalFee += fee;
    }
```
To:
```solidity
    for (uint8 i = 0; i < fees.length; i++) { 
        uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
        totalFee += fee;
        _transferTo(paymentToken, from, fees[i].recipient, fee);
    }
```