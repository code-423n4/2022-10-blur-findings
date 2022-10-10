# QA Report (low / non-critical)
## Found [4]

### [Q-01] Require Statement Missing Error Messages

Error messages help with troubleshooting, some require statements are missing error messages.

#### Findings:

*/contracts/BlurExchange.sol*
Line(s) [134](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134), [183](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183), [452](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L452)

```
134:	require(sell.order.side == Side.Sell);
183:	require(msg.sender == order.trader);
452:	require(msg.value == price);
```

### [Q-02] Code Duplication

#### Findings:

*/contracts/matchingPolicies/StandardPolicyERC721.sol*
Line(s) [12](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC721.sol#L12) / [38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC721.sol#L38) 

*/contracts/matchingPolicies/StandardPolicyERC1155.sol*
Line(s) [12](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC1155.sol#L12) / [38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC1155.sol#L38) 

The two functions in both `StandardPolicyERC721.sol` and `StandardPolicyERC1155.sol` are very similar and can be merged into a single function. The first check in both functions are an exact replica.

### [Q-03] Underscore Notation Improves Readability

#### Findings:

*/contracts/BlurExchange.sol*
Line(s): [59](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59)

```
59:	uint256 public constant INVERSE_BASIS_POINT = 10000;
```

**Suggested change:** 
```
59:	uint256 public constant INVERSE_BASIS_POINT = 10_000;
```

### [Q-04] Lack of Documentation

#### Findings:

*/contracts/lib/MerkleVerifier.sol*

The following functions have no documentation
Line(s): [45](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L45), [49](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L49)
```
45:	function _hashPair(bytes32 a, bytes32 b) private pure returns (bytes32) {
49:	function _efficientHash(
```

*/contracts/lib/EIP712.sol*

No functions in EIP712.sol have documentation
Line(s): [39](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L39), [55](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L55), [69](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L69), [84](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L84), [112](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L112), [124](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L124), [139](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L139)

```
39:	function _hashDomain(EIP712Domain memory eip712Domain)
55:	function _hashFee(Fee calldata fee)
69:	function _packFees(Fee[] calldata fees)
84:	function _hashOrder(Order calldata order, uint256 nonce)
112:	function _hashToSign(bytes32 orderHash)
124:	function _hashToSignRoot(bytes32 root)
139:	function _hashToSignOracle(bytes32 orderHash, uint256 blockNumber)
```