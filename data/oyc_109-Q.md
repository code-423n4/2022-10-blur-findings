## [L-01] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-10-blur/contracts/BlurExchange.sol::283 => return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);
```

## [L-02] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

```
2022-10-blur/contracts/lib/EIP712.sol::80 => return keccak256(abi.encodePacked(feeHashes));
2022-10-blur/contracts/lib/EIP712.sol::117 => return keccak256(abi.encodePacked(
2022-10-blur/contracts/lib/EIP712.sol::129 => return keccak256(abi.encodePacked(
2022-10-blur/contracts/lib/EIP712.sol::144 => return keccak256(abi.encodePacked(
```

## [L-03] require() should be used instead of assert()

require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking

```
2022-10-blur/contracts/lib/ERC1967Proxy.sol::22 => assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
```

## [L-04] Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
2022-10-blur/contracts/ExecutionDelegate.sol::78 => IERC721(collection).transferFrom(from, to, tokenId);
2022-10-blur/contracts/ExecutionDelegate.sol::125 => return IERC20(token).transferFrom(from, to, amount);
```

## [L-05] ecrecover() not checked for signer address of zero

The ecrecover() function returns an address of zero when the signature does not match. This can cause problems if address zero is ever the owner of assets, and someone uses the permit function on address zero. If that happens, any invalid signature will pass the checks, and the assets will be stealable. In this case, the asset of concern is the vault's ERC20 token, and fortunately OpenZeppelin's implementation does a good job of making sure that address zero is never able to have a positive balance. If this contract ever changes to another ERC20 implementation that is laxer in its checks in favor of saving gas, this code may become a problem.

```
2022-10-blur/contracts/BlurExchange.sol::408 => return ecrecover(digest, v, r, s);
```

## [L-06] Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

__gap is empty reserved space in storage that is recommended to be put in place in upgradeable contracts. It allows new state variables to be added in the future without compromising the storage compatibility with existing deployments

```
2022-10-blur/contracts/BlurExchange.sol::5 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

## [L-07] Implementation contract may not be initialized

Implementation contract does not have a constructor with the initializer modifier therefore may be uninitialized. Implementation contracts should be initialized to avoid potential griefs or exploits.

```
2022-10-blur/contracts/BlurExchange.sol::5 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```

## [N-01] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
2022-10-blur/contracts/BlurExchange.sol::59 => uint256 public constant INVERSE_BASIS_POINT = 10000;
```

## [N-02] require()/revert() statements should have descriptive reason strings

Descriptive reason strings should be used so that users can troubleshot any reverted calls

```
2022-10-blur/contracts/BlurExchange.sol::134 => require(sell.order.side == Side.Sell);
2022-10-blur/contracts/BlurExchange.sol::183 => require(msg.sender == order.trader);
2022-10-blur/contracts/BlurExchange.sol::452 => require(msg.value == price);
2022-10-blur/contracts/BlurExchange.sol::513 => revert("Invalid payment token");
```

## [N-03] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-10-blur/contracts/BlurExchange.sol::85 => event OrderCancelled(bytes32 hash);
2022-10-blur/contracts/BlurExchange.sol::86 => event NonceIncremented(address trader, uint256 newNonce);
2022-10-blur/contracts/BlurExchange.sol::88 => event NewExecutionDelegate(IExecutionDelegate executionDelegate);
2022-10-blur/contracts/BlurExchange.sol::89 => event NewPolicyManager(IPolicyManager policyManager);
2022-10-blur/contracts/BlurExchange.sol::90 => event NewOracle(address oracle);
2022-10-blur/contracts/BlurExchange.sol::91 => event NewBlockRange(uint256 blockRange);
```

## [N-04] Missing NatSpec

Code should include NatSpec

```
2022-10-blur/contracts/lib/OrderStructs.sol::1 => // SPDX-License-Identifier: MIT
```

## [N-05] Adding a return statement when the function defines a named return variable, is redundant

It is not necessary to have both a named return and a return statement.

```
2022-10-blur/contracts/BlurExchange.sol::419 => returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)
2022-10-blur/contracts/lib/ERC1967Proxy.sol::29 => function _implementation() internal view virtual override returns (address impl) {
```

## [N-07] Consider addings checks for signature malleability

Use OpenZeppelin's ECDSA contract rather than calling ecrecover() directly

```
2022-10-blur/contracts/BlurExchange.sol::408 => return ecrecover(digest, v, r, s);
```

## [N-08] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

instances:

```
2022-10-blur/contracts/lib/EIP712.sol::20 => bytes32 constant public FEE_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::23 => bytes32 constant public ORDER_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::26 => bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::29 => bytes32 constant public ROOT_TYPEHASH = keccak256(
2022-10-blur/contracts/lib/EIP712.sol::33 => bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
```

## [N-09] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2022-10-blur/contracts/lib/EIP712.sol::24 => "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
2022-10-blur/contracts/lib/EIP712.sol::27 => "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
```
