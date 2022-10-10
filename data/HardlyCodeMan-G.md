## 2022 10 Blur
### Gas Report

### Reorder struct to save storage slots

[lib/OrderStructs.sol#L30-38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/OrderStructs.sol#L30-38) : Consider re-ordering storage values to fit 32byte storage locations especially given the reuse of the structs over multiple contracts
https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

```solidity
struct Input {
    Order order;                    // 14 slots
    uint8 v;                        // 8
    bytes32 r;                      // 32
    bytes32 s;                      // 32
    bytes extraSignature;           // arbitrary 20
    SignatureVersion signatureVersion;  // 16
    uint256 blockNumber;            // 32
}                                   // 20 slots, maybe have uint8 v last following SignatureVersion signatureVersion to save 1 slot
```
Would become:
```solidity
struct Input {
    Order order;                    // 14 slots
    bytes32 r;                      // 1 slot
    bytes32 s;                      // 1 slot
    bytes extraSignature;           // 1 slot
    uint256 blockNumber;            // 1 slot
    SignatureVersion signatureVersion;  // 0.5 slot
    uint8 v;                        // 0.25 slot
}                                   // 19 slots
```

### Reorder variables to save storage slots

[BlurExchange.sol#L33](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L33) : Consider smaller storage size for a single digit like uint8 and put in a slot next to something < 24bytes like an address (20 bytes)
```solidity
    uint256 public isOpen;
```

[BlurExchange.sol#L63-L67](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L63-L67) : Perhaps swap lines 63 & 67 and place line 33 after
```solidity
    /* Variables */
    uint256 public blockRange;
    IExecutionDelegate public executionDelegate;
    IPolicyManager public policyManager;
    address public oracle;
    address public weth;
    uint8 public isOpen;
```

### Unnecessary checked arithmetic in for loops

In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save 77 gas per iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

From:
```solidity
for ( i = 0; i < someUint; i++ ){
    doSomething();
}
```
To:
```solidity
for ( i = 0; i < someUint; ){
    doSomething();
    unchecked { i++; }
}
```

- [BlurExchange.sol#L199](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199) : function cancelOrders()
- [BlurExchange.sol#L476](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476) : function _transferFees()
- [PolicyManager.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77) : function viewWhitelistedPolicies()
- [lib/EIP712.sol#L77](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77) : function _packFees()
- [lib/MerkleVerifier.sol#L38](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38) : function _computeRoot()