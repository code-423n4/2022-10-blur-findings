## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Upgradeable contract not initialized | 1 |
| [L&#x2011;02] | Unsafe use of `transfer()`/`transferFrom()` with `IERC20` | 1 |
| [L&#x2011;03] | Return values of `transfer()`/`transferFrom()` not checked | 1 |
| [L&#x2011;04] | `require()` should be used instead of `assert()` | 1 |
| [L&#x2011;05] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 1 |
| [L&#x2011;06] | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 1 |
| [L&#x2011;07] | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 1 |

Total: 7 instances over 7 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `require()`/`revert()` statements should have descriptive reason strings | 3 |
| [N&#x2011;02] | `public` functions not called by the contract should be declared `external` instead | 2 |
| [N&#x2011;03] | Non-assembly method available | 1 |
| [N&#x2011;04] | `constant`s should be defined rather than using magic numbers | 6 |
| [N&#x2011;05] | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 5 |
| [N&#x2011;06] | Lines are too long | 2 |
| [N&#x2011;07] | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 1 |
| [N&#x2011;08] | File is missing NatSpec | 1 |
| [N&#x2011;09] | NatSpec is incomplete | 15 |
| [N&#x2011;10] | Event is missing `indexed` fields | 7 |
| [N&#x2011;11] | Not using the named return variables anywhere in the function is confusing | 4 |
| [N&#x2011;12] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 1 |

Total: 48 instances over 12 issues

## Low Risk Issues

### [L&#x2011;01]  Upgradeable contract not initialized
Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user

*There is 1 instance of this issue:*
```solidity
File: contracts/BlurExchange.sol

/// @audit missing __UUPS_init()
30:   contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L30

### [L&#x2011;02]  Unsafe use of `transfer()`/`transferFrom()` with `IERC20`
Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens.  For example Tether (USDT)'s `transfer()` and `transferFrom()` functions on L1 do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to `IERC20`, their [function signatures](https://medium.com/coinmonks/missing-return-value-bug-at-least-130-tokens-affected-d67bf08521ca) do not match and therefore the calls made, revert (see [this](https://gist.github.com/IllIllI000/2b00a32e8f0559e8f386ea4f1800abc5) link for a test case). Use OpenZeppelin’s `SafeERC20`'s `safeTransfer()`/`safeTransferFrom()` instead

While this contract is currently only used with WETH, there's nothing preventing it from being used with other tokens in other contexts

*There is 1 instance of this issue:*
```solidity
File: contracts/ExecutionDelegate.sol

125:          return IERC20(token).transferFrom(from, to, amount);

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L125

### [L&#x2011;03]  Return values of `transfer()`/`transferFrom()` not checked
Not all `IERC20` implementations `revert()` when there's a failure in `transfer()`/`transferFrom()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment

While this contract is currently only used with WETH, there's nothing preventing it from being used with other tokens in other contexts

*There is 1 instance of this issue:*
```solidity
File: contracts/ExecutionDelegate.sol

125:          return IERC20(token).transferFrom(from, to, amount);

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L125

### [L&#x2011;04]  `require()` should be used instead of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

*There is 1 instance of this issue:*
```solidity
File: contracts/lib/ERC1967Proxy.sol

22:           assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L22

### [L&#x2011;05]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There is 1 instance of this issue:*
```solidity
File: contracts/BlurExchange.sol

113:          weth = _weth;

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113

### [L&#x2011;06]  `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).

If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*There is 1 instance of this issue:*
```solidity
File: contracts/lib/EIP712.sol

80:           return keccak256(abi.encodePacked(feeHashes));

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L80

### [L&#x2011;07]  Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*There is 1 instance of this issue:*
```solidity
File: contracts/BlurExchange.sol

30:   contract BlurExchange is IBlurExchange, ReentrancyGuarded, EIP712, OwnableUpgradeable, UUPSUpgradeable {

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L30

## Non-critical Issues

### [N&#x2011;01]  `require()`/`revert()` statements should have descriptive reason strings

*There are 3 instances of this issue:*
```solidity
File: contracts/BlurExchange.sol

134:          require(sell.order.side == Side.Sell);

183:          require(msg.sender == order.trader);

452:              require(msg.value == price);

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134

### [N&#x2011;02]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 2 instances of this issue:*
```solidity
File: contracts/BlurExchange.sol

95        function initialize(
96            uint chainId,
97            address _weth,
98            IExecutionDelegate _executionDelegate,
99            IPolicyManager _policyManager,
100           address _oracle,
101           uint _blockRange
102:      ) public initializer {

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L95-L102

```solidity
File: contracts/lib/MerkleVerifier.sol

17        function _verifyProof(
18            bytes32 leaf,
19            bytes32 root,
20:           bytes32[] memory proof

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L17-L20

### [N&#x2011;03]  Non-assembly method available 
`assembly{ id := chainid() }` => `uint256 id = block.chainid`, `assembly { size := extcodesize() }` => `uint256 size = address().code.length`
There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary

*There is 1 instance of this issue:*
```solidity
File: contracts/BlurExchange.sol

555:              size := extcodesize(what)

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L555

### [N&#x2011;04]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 6 instances of this issue:*
```solidity
File: contracts/BlurExchange.sol

/// @audit 27
/// @audit 28
407:          require(v == 27 || v == 28, "Invalid v parameter");

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L407

```solidity
File: contracts/lib/MerkleVerifier.sol

/// @audit 0x00
54:               mstore(0x00, a)

/// @audit 0x20
55:               mstore(0x20, b)

/// @audit 0x00
/// @audit 0x40
56:               value := keccak256(0x00, 0x40)

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L54

### [N&#x2011;05]  Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

*There are 5 instances of this issue:*
```solidity
File: contracts/lib/EIP712.sol

20        bytes32 constant public FEE_TYPEHASH = keccak256(
21            "Fee(uint16 rate,address recipient)"
22:       );

23        bytes32 constant public ORDER_TYPEHASH = keccak256(
24            "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
25:       );

26        bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
27            "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
28:       );

29        bytes32 constant public ROOT_TYPEHASH = keccak256(
30            "Root(bytes32 root)"
31:       );

33        bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
34            "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
35:       );

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20-L22

### [N&#x2011;06]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 2 instances of this issue:*
```solidity
File: contracts/lib/EIP712.sol

24:           "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"

27:           "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L24

### [N&#x2011;07]  Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables
If the variable needs to be different based on which class it comes from, a `view`/`pure` _function_ should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

*There is 1 instance of this issue:*
```solidity
File: contracts/lib/EIP712.sol

37:       bytes32 DOMAIN_SEPARATOR;

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L37

### [N&#x2011;08]  File is missing NatSpec

*There is 1 instance of this issue:*
```solidity
File: contracts/lib/OrderStructs.sol


```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/OrderStructs.sol

### [N&#x2011;09]  NatSpec is incomplete

*There are 15 instances of this issue:*
```solidity
File: contracts/BlurExchange.sol

/// @audit Missing: '@return'
256        * @param orderHash hash of order
257        */
258       function _validateOrderParameters(Order calldata order, bytes32 orderHash)
259           internal
260           view
261:          returns (bool)

/// @audit Missing: '@return'
276        * @param expirationTime order expiration time
277        */
278       function _canSettleOrder(uint256 listingTime, uint256 expirationTime)
279           view
280           internal
281:          returns (bool)

/// @audit Missing: '@return'
289        * @param orderHash hash of order
290        */
291       function _validateSignatures(Input calldata order, bytes32 orderHash)
292           internal
293           view
294:          returns (bool)

/// @audit Missing: '@return'
342        * @param extraSignature packed merkle path
343        */
344       function _validateUserAuthorization(
345           bytes32 orderHash,
346           address trader,
347           uint8 v,
348           bytes32 r,
349           bytes32 s,
350           SignatureVersion signatureVersion,
351           bytes calldata extraSignature
352:      ) internal view returns (bool) {

/// @audit Missing: '@return'
373        * @param blockNumber block number used in oracle signature
374        */
375       function _validateOracleAuthorization(
376           bytes32 orderHash,
377           SignatureVersion signatureVersion,
378           bytes calldata extraSignature,
379           uint256 blockNumber
380:      ) internal view returns (bool) {

/// @audit Missing: '@param digest'
/// @audit Missing: '@return'
395       /**
396        * @dev Wrapped ecrecover with safety check for v parameter
397        * @param v v
398        * @param r r
399        * @param s s
400        */
401       function _recover(
402           bytes32 digest,
403           uint8 v,
404           bytes32 r,
405           bytes32 s
406:      ) internal pure returns (address) {

/// @audit Missing: '@return'
414        * @param buy buy order
415        */
416       function _canMatchOrders(Order calldata sell, Order calldata buy)
417           internal
418           view
419:          returns (uint256 price, uint256 tokenId, uint256 amount, AssetType assetType)

/// @audit Missing: '@return'
467        * @param price price of token
468        */
469       function _transferFees(
470           Fee[] calldata fees,
471           address paymentToken,
472           address from,
473           uint256 price
474:      ) internal returns (uint256) {

/// @audit Missing: '@param amount'
517       /**
518        * @dev Execute call through delegate proxy
519        * @param collection collection contract address
520        * @param from seller address
521        * @param to buyer address
522        * @param tokenId tokenId
523        * @param assetType asset type of the token
524        */
525       function _executeTokenTransfer(
526           address collection,
527           address from,
528           address to,
529           uint256 tokenId,
530           uint256 amount,
531:          AssetType assetType

/// @audit Missing: '@return'
546        * @param what address to check
547        */
548       function _exists(address what)
549           internal
550           view
551:          returns (bool)

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L256-L261

```solidity
File: contracts/ExecutionDelegate.sol

/// @audit Missing: '@return'
117        * @param amount amount
118        */
119       function transferERC20(address token, address from, address to, uint256 amount)
120           approvedContract
121           external
122:          returns (bool)

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L117-L122

```solidity
File: contracts/lib/MerkleVerifier.sol

/// @audit Missing: '@return'
31         * @param proof proof
32         */
33        function _computeRoot(
34            bytes32 leaf,
35            bytes32[] memory proof
36:       ) public pure returns (bytes32) {

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L31-L36

```solidity
File: contracts/PolicyManager.sol

/// @audit Missing: '@return'
45         * @param policy address of the policy to check
46         */
47:       function isPolicyWhitelisted(address policy) external view override returns (bool) {

/// @audit Missing: '@return'
61         * @param size size
62         */
63        function viewWhitelistedPolicies(uint256 cursor, uint256 size)
64            external
65            view
66            override
67:           returns (address[] memory, uint256)

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L45-L47

### [N&#x2011;10]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 7 instances of this issue:*
```solidity
File: contracts/BlurExchange.sol

76        event OrdersMatched(
77            address indexed maker,
78            address indexed taker,
79            Order sell,
80            bytes32 sellHash,
81            Order buy,
82            bytes32 buyHash
83:       );

85:       event OrderCancelled(bytes32 hash);

86:       event NonceIncremented(address trader, uint256 newNonce);

88:       event NewExecutionDelegate(IExecutionDelegate executionDelegate);

89:       event NewPolicyManager(IPolicyManager policyManager);

90:       event NewOracle(address oracle);

91:       event NewBlockRange(uint256 blockRange);

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L76-L83

### [N&#x2011;11]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 4 instances of this issue:*
```solidity
File: contracts/lib/EIP712.sol

/// @audit hash
112       function _hashToSign(bytes32 orderHash)
113           internal
114           view
115:          returns (bytes32 hash)

/// @audit hash
124       function _hashToSignRoot(bytes32 root)
125           internal
126           view
127:          returns (bytes32 hash)

/// @audit hash
139       function _hashToSignOracle(bytes32 orderHash, uint256 blockNumber)
140           internal
141           view
142:          returns (bytes32 hash)

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L112-L115

```solidity
File: contracts/lib/ERC1967Proxy.sol

/// @audit impl
29:       function _implementation() internal view virtual override returns (address impl) {

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L29

### [N&#x2011;12]  Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions

*There is 1 instance of this issue:*
```solidity
File: contracts/ExecutionDelegate.sol

92:           require(revokedApproval[from] == false, "User has revoked approval");

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92



