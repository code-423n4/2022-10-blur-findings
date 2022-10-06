

### [L01] require() should be used instead of assert()


#### Findings:
```
2022-10-blur/contracts/lib/ERC1967Proxy.sol::22 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
```



### [L02] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::113 => weth = _weth;
2022-10-blur/contracts/BlurExchange.sol::114 => executionDelegate = _executionDelegate;
2022-10-blur/contracts/BlurExchange.sol::115 => policyManager = _policyManager;
2022-10-blur/contracts/BlurExchange.sol::116 => oracle = _oracle;
2022-10-blur/contracts/BlurExchange.sol::117 => blockRange = _blockRange;
2022-10-blur/contracts/BlurExchange.sol::220 => executionDelegate = _executionDelegate;
2022-10-blur/contracts/BlurExchange.sol::228 => require(address(_policyManager) != address(0), "Address cannot be zero");
2022-10-blur/contracts/BlurExchange.sol::229 => policyManager = _policyManager;
2022-10-blur/contracts/BlurExchange.sol::238 => oracle = _oracle;
2022-10-blur/contracts/BlurExchange.sol::246 => blockRange = _blockRange;
2022-10-blur/contracts/BlurExchange.sol::356 => hashToSign = _hashToSign(orderHash);
2022-10-blur/contracts/BlurExchange.sol::362 => hashToSign = _hashToSignRoot(computedRoot);
```



### [L03] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::95 => function initialize(
2022-10-blur/contracts/BlurExchange.sol::102 => ) public initializer {
```



### [L04] Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

#### Impact
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps)
 link for a description of this storage variable. While some contracts 
may not currently be sub-classed, adding the variable now protects 
against forgetting to add it in the future.
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::30 => contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {
```



### [L05] `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`

#### Impact
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.
#### Findings:
```
2022-10-blur/contracts/lib/EIP712.sol::80 => return keccak256(abi.encodePacked(feeHashes));
2022-10-blur/contracts/lib/EIP712.sol::117 => return keccak256(abi.encodePacked(
2022-10-blur/contracts/lib/EIP712.sol::129 => return keccak256(abi.encodePacked(
2022-10-blur/contracts/lib/EIP712.sol::144 => return keccak256(abi.encodePacked(
```



### [L06] `address.call{value:x}()` should be used instead of `payable.transfer()`

#### Impact
The use of `payable.transfer()` is heavily frowned upon because it can lead to the locking of funds. The `transfer()` call requires that the recipient has a `payable` callback, only provides 2300 gas for its operation. This means the following cases can cause the transfer to fail:

- The contract does not have a `payable` callback
- The contract’s `payable` callback spends more than 2300 gas (which is only enough to emit something)
- The contract is called through a proxy which itself uses up the 2300 gas
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::508 => payable(to).transfer(amount);
```



### [L07] Return values of `transfer()`/`transferFrom()` not checked

#### Impact
Not all IERC20 implementations revert() when there’s a failure in transfer()/transferFrom(). The function signature has a boolean
 return value and they indicate errors that way instead. By not checking
 the return value, operations that should have marked as failed, may 
potentially go through without actually making a payment

#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::508 => payable(to).transfer(amount);
2022-10-blur/contracts/ExecutionDelegate.sol::78 => IERC721(collection).transferFrom(from, to, tokenId);
2022-10-blur/contracts/ExecutionDelegate.sol::125 => return IERC20(token).transferFrom(from, to, amount);
```








##### Non-Critical Issues





### [N01] `require()`/`revert()` statements should have descriptive reason strings


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::134 => require(sell.order.side == Side.Sell);
2022-10-blur/contracts/BlurExchange.sol::183 => require(msg.sender == order.trader);
2022-10-blur/contracts/BlurExchange.sol::452 => require(msg.value == price);
```



### [N02] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::86 => event NonceIncremented(address trader, uint256 newNonce);
```



### [N03] Use of sensitive/NC-inclusive terms


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::421 => bool canMatch;
```



### [N04] Unused file


#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::1 => // SPDX-License-Identifier: MIT
2022-10-blur/contracts/ExecutionDelegate.sol::1 => // SPDX-License-Identifier: MIT
2022-10-blur/contracts/PolicyManager.sol::1 => // SPDX-License-Identifier: MIT
2022-10-blur/contracts/lib/EIP712.sol::1 => // SPDX-License-Identifier: MIT
2022-10-blur/contracts/lib/ERC1967Proxy.sol::1 => // SPDX-License-Identifier: MIT
2022-10-blur/contracts/lib/MerkleVerifier.sol::1 => // SPDX-License-Identifier: MIT
2022-10-blur/contracts/lib/OrderStructs.sol::1 => // SPDX-License-Identifier: MIT
2022-10-blur/contracts/lib/ReentrancyGuarded.sol::1 => // SPDX-License-Identifier: MIT
2022-10-blur/contracts/matchingPolicies/StandardPolicyERC1155.sol::1 => // SPDX-License-Identifier: MIT
2022-10-blur/contracts/matchingPolicies/StandardPolicyERC721.sol::1 => // SPDX-License-Identifier: MIT
```



### [N05] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external.
#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::181 => function cancelOrder(Order calldata order) public {
```



### [N06] Constant redefined elsewhere

#### Impact
Consider defining in only one contract so that values cannot become out of sync when only one location is updated
#### Findings:
```
2022-10-blur/contracts/lib/EIP712.sol::20 => bytes32 constant public FEE_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::23 => bytes32 constant public ORDER_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::26 => bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::29 => bytes32 constant public ROOT_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::33 => bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
```








