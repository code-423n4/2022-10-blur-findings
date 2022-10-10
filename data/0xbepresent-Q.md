1 - Adding a return statement when the function defines a named return variable, is redundant
==
Instances of this issue:

```
contracts/BlurExchange.sol#L433         return (price, tokenId, amount, assetType);
```

**Links:**

[contracts/BlurExchange.sol#L433](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L433)


2 - Event is missing indexed fields
==

The trader address could be indexed

Instances of this issue:

```
contracts/BlurExchange.sol#L86          event NonceIncremented(address trader, uint256 newNonce);
```

**Links:**

[contracts/BlurExchange.sol#L86](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L86)