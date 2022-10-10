# [01] Add a check to ensure the return of `ecrecover` is not `address(0)` and consider replacing it with OpenZeppelin’s `ECDSA`

## Impact

EVM’s ecrecover is susceptible to signature malleability, which allows replay attacks.

[SWC Registry](https://swcregistry.io/docs/SWC-117)

"A malicious user can slightly modify the three values v, r and s to create other valid signatures. A system that performs signature verification on contract level might be susceptible to attacks if the signature is part of the signed message hash. Valid signatures could be created by a malicious user to replay previously signed messages"

Additionally, if the return of `ecrecover` and `trader/oracle` are address zero, the `user/oracle` authorization will pass. The correct behavior would be to revert if `ecrecover` returns address zero, since the signature provided will be invalid.

## Proof of Concept

`BlurExchange` is using ecrecoever to validate the user and oracle signatures.

```
return _recover(hashToSign, v, r, s) == trader;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L365

```
return _recover(oracleHash, v, r, s) == oracle;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L392

```
return ecrecover(digest, v, r, s);
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L408

## Recommended Mitigation Steps

Replace `ecrecover` it with [OpenZeppelin’s ECDSA library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol) to prevent replay attacks, and check if the return of the signature is not address zero.

# [02] Check the return of `weth` token transfer

Silent failures of transfers can affect token accounting in contract.

[Reference](https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iwethtransfer-call) from Consensys audit of Fei protocol.

## Proof of Concept

```
executionDelegate.transferERC20(weth, from, to, amount); 
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L511

```
return IERC20(token).transferFrom(from, to, amount);
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L125

## Recommended Mitigation Steps

Add a require statement to check the return of IERC20 transfer or use OpenZeppelin `safeTransfer`.

# [03] Not checking if an order exists before cancelling can cause to silent failures

For `BlurExchange.cancelOrder`, the state variable `cancelledOrFilled` will be a mapping where the default value for any hash will be false. If a valid trader passes an incorrect order, the cancellation will be succesful. The impact is that the user won't receive any feedback from the failure, and might not cancel the desired order.

```
function cancelOrder(Order calldata order) public {
    /* Assert sender is authorized to cancel order. */
    require(msg.sender == order.trader);

    bytes32 hash = _hashOrder(order, nonces[order.trader]);

    if (!cancelledOrFilled[hash]) {
        /* Mark order as cancelled, preventing it from being matched. */
        cancelledOrFilled[hash] = true;
        emit OrderCancelled(hash);
    }
}
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L181-L192

## Recommendation

If possible, check if the order exists before canceling. If the order doesn't exist, the optimal behavior for `cancelOrder` would be to revert. This can also help preventing future orders from being canceled (since it will check if it exists before).

# [04] Missing storage gap for upgradeable contracts

Adding a gap [protects](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) against storage variable issues on upgradable contracts. See [similar issue](https://blog.openzeppelin.com/notional-audit/) for OpenZeppelin audit of Notional (second medium item in the audit).

```
contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L30

## Recommendation

Consider adding a __gap[] storage variable to allow for new storage variables in later versions. Check [here](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Gap10000.sol) for an implementation example.

# [05] Replace assert with require or custom error

Assert should be avoided in production code. As described on the solidity [documentation](https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require). "The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix."

Even if the code is expected to be unreacheable, consider using a require statement or a custom error instead of assert.

```
File: contracts/lib/ERC1967Proxy.sol
22: assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
```

# [06] Use the latest version of solidity

## Impact

All the contracts in scope are using `v0.8.13`.

This version has a problem related to passing nested calldata arrays into `abi.encode` - not applicable to Blur, since the array passed into EPI712 is not nested. [Reference](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L80).

One problem that is applicable is related to an inline assembly bug when the optimizer is enabled. This project is using inline assembly and most importantly is inheriting contracts from OpenZeppelin which uses inline assembly. Also, the optimizer is enabled. [Vulnerability reference](https://blog.soliditylang.org/2022/06/15/inline-assembly-memory-side-effects-bug/).

## Recommended Mitigation Steps

Consider using the latest version of solidity to ensure the compiler has the latest security fixes

# [07] There is no support for cryptopunks

## Impact

Cryptopunks are important for the crypto-collectibles movement, but they do not follow the ERC721 standard.

## Recommended Mitigation Steps

To integrate cryptopunks into the exchange, refer to this [example](https://github.com/code-423n4/2021-12-nftx/blob/main/nftx-protocol-v2/contracts/solidity/NFTXStakingZap.sol#L417-L424).

Alternatively, if the idea is to not support cryptopunks, consider documenting it and warning end users that the exchange does not support such items.

# [08] Missing zero address checks for `initializer`

```
function initialize(
    ...
    address _weth, 
    ...
    address _oracle,
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95-L100

Oracle has a zero address check on `setOracle()`. Consider adding it one on `initialize()` as well.

Also, if `weth` is initialized incorrectly, it will cause the need for reployment. Adding a zero address check for `weth` can help to avoid the need of redeployments for the `BlurExchange.sol` contract. Adding a setter fundtion for `weth` can also help.

If an variable gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

# [09] Missing empty constructor with `initializer` modifier for contracts using `UUPSUpgradeable`

OpenZeppelin recommends that the initializer modifier be applied to constructors in order to avoid potential griefs, social engineering, or exploits. Ensure that the modifier is applied to the implementation contract.

More information about this bug at [post-mortem](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680) and [security advisory](https://forum.openzeppelin.com/t/security-advisory-initialize-uups-implementation-contracts/15301/14).

Also, `BlurExchange` is not inheriting `Initializable`, but it's using the modifier on `initialize()`. Is possible, apply the following changes:

```
diff --git a/BlurExchange.sol.orig b/BlurExchange.sol
index ba7057b..0992e17 100644
--- a/BlurExchange.sol.orig
+++ b/BlurExchange.sol
-contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {
+contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable, Initializable {
```

# [10] Update OpenZeppelin version

The project is using `@openzeppelin/contracts: 4.4.1`. The lastest version of OpenZeppelin is `4.7.3`. 

Consider updating to the latest version or adding a `^` to round the minor and patch versions, similarly to what is currently being done in `@openzeppelin/contracts-upgradeable": ^4.6.0`.

# [11] Critical changes should use two-step procedure

The critical procedures should be two step process.

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

```
function setExecutionDelegate(IExecutionDelegate _executionDelegate)
    external
    onlyOwner
{
    require(address(_executionDelegate) != address(0), "Address cannot be zero");
    executionDelegate = _executionDelegate;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L215-L220

```
function setPolicyManager(IPolicyManager _policyManager)
    external
    onlyOwner
{
    require(address(_policyManager) != address(0), "Address cannot be zero");
    policyManager = _policyManager;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L224-L229

```
function setOracle(address _oracle)
    external
    onlyOwner
{
    require(_oracle != address(0), "Address cannot be zero");
    oracle = _oracle;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L233-L238

## Recommended Mitigation Steps

Consider adding two-steps functionalitiy on critical changes to avoid malicious takeovers. Pseudocode for two-steps pattern is highlighted bellow.

```
address oracle;
address pendingOracle;

function setPendingOracle(address pendingOracle) external onlyOwner {
    require(_oracle != address(0), "Address cannot be zero");
    pendingOracle = pendingOracle;
    emit NewPendingOracle(pendingOracle);
}

function acceptOracle() external onlyOwner {
    require(pendingOracle != address(0), "!pendingOracle");
    oracle = pendingOracle;
    pendingOracle = address(0);
    emit newOracle(oracle);
}
```

# [12] Using return named variables and manually returning the values for the same function.

Consider either returning the values manually and not using the return named variable feature, or using the feature and not returning the values manually.

```
function _canMatchOrders(Order calldata sell, Order calldata buy)
    internal
    view
    returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)
{
    ...
    return (price, tokenId, amount, assetType);
}
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L416-L434

# [13] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

The instance bellow shows public above external.

```
function cancelOrder(Order calldata order) public {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L181

```
function cancelOrders(Order[] calldata orders) external {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L198

# [14] Add a limit for the maximum number of characters per line

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum of 120 characters.

Consider adding a limit of 120 characters or less to prevent large lines.

```
(bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388

# [15] Remove unnecessary parenthesis

```
return (receiveAmount);
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L486

# [16] Avoid using `uint256` and `uint` interchangeably

Following instances are using `uint`.

```
uint chainId,
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L96

```
uint _blockRange
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L101


```
uint size;
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L553

The remaining of the codebase is using `uint256`. E.g.

```
function _canSettleOrder(uint256 listingTime, uint256 expirationTime)
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L278

## Recommendation

Consider using only one approach, e.g. using only `uint256` or `uint` on all instances.

# [17] Missing NATSPEC/documentation

Consider adding NATSPEC on all functions to improve documentation. Specifically, the contract `EIP712.sol` is missing documentation. E.g.

```
function _hashOrder(Order calldata order, uint256 nonce)
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L84

# [18] Contract should be abstract

A utility contract that is not expected to be deployed as a standalone contract should be declared as abstract.

```
contract EIP712 {
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L10

# [19] Usage of uint8 for `cancelOrders()` loop

Currently, it's only possible to cancel a maximum of 2^8 orders at once. 

Since the for loop in `cancelOrder()` does not contain external calls, increasing the number of loops shoudn't cause a out-of-gas error. Hence, it's possible to use a bigger uint in the loop (e.g. uint16 = 65535), to prevent a small number being used a upper bound.


Alternatively, consider documenting the design decision behind `uint8`.

```
function cancelOrders(Order[] calldata orders) external {
    for (uint8 i = 0; i < orders.length; i++) {
        cancelOrder(orders[i]);
    }
}
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L198-L202
