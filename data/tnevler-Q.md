# Report
## Low Risk ##

### [L-01]: Missing checks for address(0x0) when assigning values to address state variables
**Context:** 

1. ``` weth = _weth; ``` [L113](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L113)
2. ``` oracle = _oracle; ``` [L116](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L116)

## Non-Critical Issues ##

### [N-01]: Adding a return statement when the function defines a named return variable, is redundant
 
1. ``` return ERC1967Upgrade._getImplementation(); ``` [L30](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ERC1967Proxy.sol#L30)
2. ``` return keccak256(abi.encodePacked( ``` [L117-L121](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L117-L121)
3. ``` return keccak256(abi.encodePacked( ``` [L129-L136](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L129-L136)
4. ``` return keccak256(abi.encodePacked( ``` [L144-L152](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L144-L152)
5. ``` return (price, tokenId, amount, assetType); ``` [L433](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L433)

### [N-02]: Multiple spaces

1. ``` string  name; ``` [L13](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L13)
2. ``` string  version; ``` [L14](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L14) 

Remove odd spaces.

### [N-03]: Wrong order of functions
**Context:** 

[BlurExchange.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol)

**Description:**

According [official solidity documentation](https://docs.soliditylang.org/en/v0.8.6/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

**Recommendation:**

Put the functions in the correct order according to the documentation.


### [N-04]: Public function can be external
**Context:** 

[MerkleVerifier._verifyProof](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17)

**Description:**

Public functions can be declared external if they are not called by the contract.

**Recommendation:**

Declare these functions as external instead of public.

### [N-05]: Require() statements should have descriptive reason string 

1. ``` require(sell.order.side == Side.Sell); ``` [L134](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134)
2. ``` require(msg.sender == order.trader); ``` [L183](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183)
3. ``` require(msg.value == price); ``` [L452](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L452)


### [N-06]: NatSpec is missing

1. [StandardPolicyERC1155.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC1155.sol)
2. [StandardPolicyERC721.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC721.sol)
3. [EIP712.sol](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol)
4. [MerkleVerifier._efficientHash](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L49)
5. [MerkleVerifier._hashPair](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L45)
6. [BlurExchange.open](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L43)
7. [BlurExchange.close](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L47)
8. [BlurExchange.setExecutionDelegate](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L215)
9. [BlurExchange.setPolicyManager](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L224)
10. [BlurExchange.setOracle](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L233)
11. [BlurExchange.setBlockRange](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L242)