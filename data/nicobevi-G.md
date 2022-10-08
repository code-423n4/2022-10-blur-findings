1. Add `indexed` to `trader` property on `NonceIncremented` event

Link: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L86

Change event definition to:
```solidity
  event NonceIncremented(address indexed trader, uint256 newNonce);
```

Reason: Improve events search and gas use.

2. Add `indexed` to `NewOracle` event.

Link: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L89

Change event definition to:
```solidity
event NewOracle(address indexed oracle);
```

Reason: Improve events search and gas use.

3. Move `_whitelistedPolicies.length()` to a variable to save gas

Lines: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L71-L73

Change to:

```solidity
uint256 whitelistedPoliciesLength = _whitelistedPolicies.length();
if (length > whitelistedPoliciesLength - cursor) {
    length = whitelistedPoliciesLength - cursor;
}
```