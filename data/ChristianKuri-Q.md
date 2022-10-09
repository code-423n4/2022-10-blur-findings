## [L-01] Use two-phase ownership transfers

Recommend considering implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

## [NC-01] large multiples of ten should use scientific notation

#### Recommendation

```solidity
// from
uint256 number = 1000000

// to
uint256 number = 1e6
```

#### Findings:

```solidity
contracts/BlurExchange.sol::59 => uint256 public constant INVERSE_BASIS_POINT = 10000;
```
