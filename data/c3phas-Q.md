## QA

### Unsafe use of transfer()/transferFrom() with IERC20

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead or check the return value.

Bad:
```solidity
IERC20(token).transferFrom(msg.sender, address(this), amount);
```
Good (using OpenZeppelin's SafeERC20):
```solidity
import {SafeERC20} from "openzeppelin/token/utils/SafeERC20.sol";

// ...

IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
```
Good (using require):
```solidity
bool success = IERC20(token).transferFrom(msg.sender, address(this), amount);
require(success, "ERC20 transfer failed");
```

Consider using safeTransfer/safeTransferFrom or wrap in a require statement here:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L119-L126
```solidity
File: /contracts/ExecutionDelegate.sol
119:    function transferERC20(address token, address from, address to, uint256 amount)
120:       approvedContract
121:        external
122:        returns (bool)
123:    {
124:        require(revokedApproval[from] == false, "User has revoked approval");
125:        return IERC20(token).transferFrom(from, to, amount);
126:    }

```

### Use of solidity's transfer() function might render ETH impossible to withdraw
When withdrawing ETH deposits, the PayableProxyController contract uses Solidity’s transfer function. This has some notable shortcomings when the withdrawer is a smart contract, which can render ETH deposits impossible to withdraw. Specifically, the withdrawal will inevitably fail when:

   1. The withdrawer smart contract does not implement a payable fallback function.
   2. The withdrawer smart contract implements a payable fallback function which uses more than 2300 gas units.
   3. The withdrawer smart contract implements a payable fallback function which needs less than 2300 gas units but is called through a proxy that raises the call’s gas usage above 2300.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L496-L515
```solidity
File: /contracts/BlurExchange.sol
496:    function _transferTo(
497:        address paymentToken,
498:        address from,
499:        address to,
500:        uint256 amount

506:        if (paymentToken == address(0)) {
507:            /* Transfer funds in ETH. */
508:            payable(to).transfer(amount);

```

To prevent unexpected behavior and potential loss of funds, consider explicitly warning end-users about the mentioned shortcomings to raise awareness before they deposit Ether into the protocol. Additionally, note that the sendValue function available in OpenZeppelin Contract’s Address library can be used to transfer the withdrawn Ether without being limited to 2300 gas units. Risks of reentrancy stemming from the use of this function can be mitigated by tightly following the “Check-effects-interactions” pattern and using OpenZeppelin Contract’s ReentrancyGuard contract. For further reference on why using Solidity’s transfer is no longer recommended, refer to these articles:

- [Stop using Solidity’s transfer now](https://diligence.consensys.net/blog/2019/09/stop-using-soliditys-transfer-now/)
- [Reentrancy after Istanbul](https://blog.openzeppelin.com/reentrancy-after-istanbul/)

### Missing checks for address(0x0) when assigning values to address state variables
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113
```solidity
File: /contracts/BlurExchange.sol
113:        weth = _weth;

116:        oracle = _oracle;
```

### Prevent accidentally burning tokens

Transferring tokens to the zero address is usually prohibited to accidentally avoid "burning" tokens by sending them to an unrecoverable zero address.

Consider adding a check to prevent accidentally burning tokens here:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L496-L515
```solidity
File: /contracts/BlurExchange.sol
496:    function _transferTo(
497:        address paymentToken,
498:        address from,
499:        address to,
500:        uint256 amount

506:        if (paymentToken == address(0)) {
507:            /* Transfer funds in ETH. */
508:            payable(to).transfer(amount);

```
Add a check for `require(to != address(0),"Cannot transfer to address 0"`

###  `ecrecover` precompile internally checks if the value is 27 or 28. No need to check in the client side.
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L401-L409
See audit tag on the specif line affected.
```solidity
File: /contracts/BlurExchange.sol
401:    function _recover(
402:        bytes32 digest,
403:        uint8 v,
404:        bytes32 r,
405:        bytes32 s
406:    ) internal pure returns (address) {
407:        require(v == 27 || v == 28, "Invalid v parameter"); // @audit this check is performed by `ecrecover` internally
408:        return ecrecover(digest, v, r, s);
409:    }
```
[See this pull request on the  Ethereum Yellow paper ](https://github.com/ethereum/yellowpaper/pull/860)

### Non-assembly method available
`assembly{ id := chainid() }` => `uint256 id = block.chainid`, `assembly { size := extcodesize() }` => `uint256 size = address().code.length`  
There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary.
**Instance**
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L548-L558
```solidity
File: /contracts/BlurExchange.sol
548:    function _exists(address what)
549:        internal
550:        view
551:        returns (bool)
552:    {
553:        uint size;
554:        assembly {
555:            size := extcodesize(what)
556:        }
557:        return size > 0;
558:    }
```

### Constants should be defined rather than using magic numbers
There are several occurrences of literal values with unexplained meaning .Literal values in the codebase without an explained meaning make the code harder to read, understand and maintain, thus hindering the experience of developers, auditors and external contributors alike.

Developers should define a constant variable for every magic value used , giving it a clear and self-explanatory name. 

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC1155.sol#L33
```solidity
File: contracts/matchingPolicies/StandardPolicyERC1155.sol

//@audit: 1
33:            1,

//@audit: 1
59:            1,
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC721.sol#L33
```solidity
File: /contracts/matchingPolicies/StandardPolicyERC721.sol

//@audit: 1
33:            1,

//@audit: 1
59:            1,
```
### abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). "Unless there is a compelling reason, abi.encode should be preferred". If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

If all arguments are strings and or bytes, bytes.concat() should be used instead

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L117-L121
```solidity
File: /contracts/lib/EIP712.sol
117:        return keccak256(abi.encodePacked(
118:            "\x19\x01",
119:            DOMAIN_SEPARATOR,
120:            orderHash
121:        ));
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L129-L136
```solidity
File: /contracts/lib/EIP712.sol
129:        return keccak256(abi.encodePacked(
130:            "\x19\x01",
131:            DOMAIN_SEPARATOR,
132:            keccak256(abi.encode(
133:                ROOT_TYPEHASH,
134:                root
135:            ))
136:        ));
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L144-L152
```solidity
File: /contracts/lib/EIP712.sol
144:        return keccak256(abi.encodePacked(
145:            "\x19\x01",
146:            DOMAIN_SEPARATOR,
147:            keccak256(abi.encode(
148:                ORACLE_ORDER_TYPEHASH,
149:                orderHash,
150:                blockNumber
151:            ))
152:        ));
```

### Public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.
Functions marked by external use call data to read arguments, where public will first allocate in local memory and then load them.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L17-L26
```solidity
File: /contracts/lib/MerkleVerifier.sol
17:    function _verifyProof(
18:        bytes32 leaf,
19:        bytes32 root,
20:        bytes32[] memory proof
21:    ) public pure {

26:    }
```

### ABI coder v2 is activated by default
As per the list of [v0.8 breaking changes](https://docs.soliditylang.org/en/v0.8.0/080-breaking-changes.html), the ABIEncoderV2 is not experimental anymore - it is actually enabled by default by the compiler.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L3
```solidity
File: /contracts/BlurExchange.sol

3:  pragma abicoder v2;

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L3
```solidity
File: /contracts/ExecutionDelegate.sol
3:  pragma abicoder v2;
```
### Uppercase Variable Named should be reserved for constants
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L37
```solidity
File: /contracts/lib/EIP712.sol
37:    bytes32 DOMAIN_SEPARATOR;
```

### Unused named return
Using both named returns and a return statement isn’t necessary in  a function.To  improve code quality, consider using only one of those.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L29-L31
```solidity
File: /contracts/lib/ERC1967Proxy.sol

//@audit: Named return, impl is not being used anywhere in the code
29:    function _implementation() internal view virtual override returns (address impl) {
30:        return ERC1967Upgrade._getImplementation();
31:    }
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L112-L115
```solidity
File: /contracts/lib/EIP712.sol

//@audit: Named return, hash is not being used anywhere
112:    function _hashToSign(bytes32 orderHash)
113:        internal
114:        view
115:        returns (bytes32 hash)
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L124-L128
```solidity
File: /contracts/lib/EIP712.sol

//@audit: Named return hash is not being used anywhere
124:    function _hashToSignRoot(bytes32 root)
125:        internal
126:        view
127:        returns (bytes32 hash)
128:    {
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L139-L143
```solidity
File: /contracts/lib/EIP712.sol

//@audit: Named return hash is not being used anywhere
139:    function _hashToSignOracle(bytes32 orderHash, uint256 blockNumber)
140:        internal
141:        view
142:        returns (bytes32 hash)
143:    {
```
### Natspec is incomplete
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L43-L47
```solidity
File: /contracts/PolicyManager.sol

//@audit: Missing @return
43:    /**
44:     * @notice Returns if a policy has been added
45:     * @param policy address of the policy to check
46:     */
47:    function isPolicyWhitelisted(address policy) external view override returns (bool) {
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC1155.sol#L12
```solidity
File: /contracts/matchingPolicies/StandardPolicyERC1155.sol

//@audit: Missing @param makerAsk, @param takerBid
12:    function canMatchMakerAsk(Order calldata makerAsk, Order calldata takerBid)
```
### Misleading/Unclear comment/Natspec
The comment says we are cancelling all current orders , the code is simply incrementing the nonce. This makes sense as all orders signed with the previous nonce are invalid  however the comment should make that explicit by stating `@dev Cancel all current orders for a user by incrementing the nonce, preventing them from being matched. Must be called by the trader of the order`

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L204-L210
```solidity
File: /contracts/BlurExchange.sol
204:    /**
205:     * @dev Cancel all current orders for a user, preventing them from being matched. Must be called by the trader of the order
206:     */
207:    function incrementNonce() external {
208:        nonces[msg.sender] += 1;
209:        emit NonceIncremented(msg.sender, nonces[msg.sender]);
210:    }

```

### Inconsistency in using uint vs uint256
`uint` is simply an alias for `uint256` . Throughout the code , the use of `uint256` seems to be the preference. The only occurrence of `uint` is the following function. For consistency I suggest refactoring this code to explicitly use `uint256` 

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L95-L102
```solidity
File: /contracts/BlurExchange.sol

//@audit: uint chainId,uint _blockRange
95:    function initialize(
96:        uint chainId,
97:        address _weth,
98:        IExecutionDelegate _executionDelegate,
99:        IPolicyManager _policyManager,
100:        address _oracle,
101:        uint _blockRange
102:    ) public initializer {

```

### Use scientific notation rather than decimal literals large multiples of ten should
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L59
```solidity
File: /contracts/BlurExchange.sol
59:    uint256 public constant INVERSE_BASIS_POINT = 10000;
```

### Lack of event emission after critical initialize() functions
Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L95-L118
```solidity
File: /contracts/BlurExchange.sol
95:    function initialize(
  
102:    ) public initializer {
103:        __Ownable_init();
104:        isOpen = 1;


118:    }
```
### Missing Friendly revert strings
 require()/revert() statements should have descriptive reason strings.
 
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134
```solidity
File: /contracts/BlurExchange.sol
134:        require(sell.order.side == Side.Sell);

183:        require(msg.sender == order.trader);

452:        require(msg.value == price);

```

### Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L85-L91
```solidity
File: /contracts/BlurExchange.sol
85:    event OrderCancelled(bytes32 hash);
86:    event NonceIncremented(address trader, uint256 newNonce);

88:    event NewExecutionDelegate(IExecutionDelegate executionDelegate);
89:    event NewPolicyManager(IPolicyManager policyManager);
90:    event NewOracle(address oracle);
91:    event NewBlockRange(uint256 blockRange);
```
