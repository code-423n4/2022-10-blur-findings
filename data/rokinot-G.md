### More efficient increment

Use `++nonces[msg.sender];` instead of `nonces[msg.sender] += 1;`

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L208

### `x = x + y` is more efficient than `x += y`

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479