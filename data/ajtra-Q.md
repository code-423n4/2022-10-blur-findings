# Low

# Details
## 1. Use of assert() instead of require() / revert()
### Description
According to solidity [documentation](https://docs.soliditylang.org/en/v0.8.0/control-structures.html#error-handling-assert-require-revert-and-exceptions) use require/revert
instead of assert.

### Mitigation
Change assert for require or revert
 
### Lines in the code
[ERC1967Proxy.sol#L22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L22)

## 2. Outdated pragma
The project is using the solidity version 0.8.13. It's a best practice to use the latest release version. You can consult it in the following [link](https://github.com/ethereum/solidity/releases)

### Mitigation 
Update the solidity version to 0.8.17

### Lines in the code
[ReentrancyGuarded.sol#L2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L2)
[ERC1967Proxy.sol#L3](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L3)
[EIP712.sol#L2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L2)
[MerkleVerifier.sol#L2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L2)
[OrderStructs.sol#L2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/OrderStructs.sol#L2)
[BlurExchange.sol#L2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L2)
[ExecutionDelegate.sol#L2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L2)
[StandardPolicyERC1155.sol#L2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC1155.sol#L2)
[StandardPolicyERC721.sol#L2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC721.sol#L2)
[PolicyManager.sol#L2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L2)
	
## 3. Missing checks for address(0x0) when assigning values to address state variables

### Mitigation
Add check for address(0x0)

### Lines in the code
[BlurExchange.sol#L113](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113)
[BlurExchange.sol#L116](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L116)

# Non Critical

## 4. Constants should be defined rather than using magic numbers
### Mitigation
Define a constant for the number

### Lines in the code
[BlurExchange.sol#L407](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L407)

## 5. public functions not called by the contract should be declared external instead
[BlurExchange.sol#L95](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L95)
[MerkleVerifier.sol#L17](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L17)
[MerkleVerifier.sol#L33](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L33)
[MerkleVerifier.sol#L49](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L49)

## 6. File is missing NatSpec
[ReentrancyGuarded.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol)
[ERC1967Proxy.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol)
[EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol)
[MerkleVerifier.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol)
[OrderStructs.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/OrderStructs.sol)
[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol)
[ExecutionDelegate.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol)
[StandardPolicyERC1155.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC1155.sol)
[StandardPolicyERC721.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC721.sol)
[PolicyManager.sol](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol)

## 7. Event is missing indexed fields
### Description
Index event fields make the field more quickly accessible to off-chain tools that parse events. 
However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (threefields). 
Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. 
If there are fewer than three fields, all of the fields should be indexed.

### Lines in the code
[BlurExchange.sol#L76](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L76)
[BlurExchange.sol#L85-L91](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L85-L91)

