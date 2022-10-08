# 2022-10-BLUR

## Low Risk and Non-Critical Issues

### Events not emmited on important state changes

Emmiting events is recommended each time when a state variable's value is being changed or just some critical event for the contract has occurred. It also helps off-chain monitoring of the contract's state.

_There are **1** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

95:   function initialize(
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

### `public` functions not called by the contract should be declared `external` instead

_There are **2** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

95:   function initialize(
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

```solidity
File: contracts/lib/MerkleVerifier.sol

17:   function _verifyProof(
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol

### Natspec is missing

_There are **1** instances of this issue:_

```solidity
File: contracts/lib/OrderStructs.sol

1:    // SPDX-License-Identifier: MIT
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/OrderStructs.sol

### Not used import

_There are **1** instances of this issue:_

```solidity
File: contracts/BlurExchange.sol

5:    import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol