# Qa Report

## BLUR

### Missing natspec comments

https://docs.soliditylang.org/en/develop/natspec-format.html

[OrderStructs.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/OrderStructs.sol):

```solidity
line#L1:   // SPDX-License-Identifier: MIT
```

### Unused import statements

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L5:   import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

### Use `external` visibility modifier for function that are not being invoked by the contract

[MerkleVerifier.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol):

```solidity
line#L17:  function _verifyProof(
```

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L95:  function initialize(
```

### Events should be emmitted on every critical state changes

Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol):

```solidity
line#L95:  function initialize(
```
