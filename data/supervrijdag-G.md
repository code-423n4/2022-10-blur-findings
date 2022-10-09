# ++ costs less gas than += 1

Changing nonces[msg.sender] += 1; to nonces[msg.sender]++ saves 15 gas.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208
```
function incrementNonce() external {
    - nonces[msg.sender] += 1;
    + ++nonces[msg.sender];
    emit NonceIncremented(msg.sender, nonces[msg.sender]);
}
```