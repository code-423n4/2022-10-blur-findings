### Typos
___
[ERC1967Proxy.sol: L19](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L19)
```solidity
     * function call, and allows initializating the storage of the proxy like a Solidity constructor.
```
Change `initializating` to `initialization of`
___
[BlurExchange.sol: L387](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L387)
```solidity
            /* If the signature was a bulk listing the merkle path musted be unpacked before the oracle signature. */
```
Change `musted` to `must`
___
___


### Named `return` variable is not used
The existence of a named return variable that is never used (here, `impl`) is potentially perplexing
___
[ERC1967Proxy.sol: L29](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L29)
```solidity
    function _implementation() internal view virtual override returns (address impl) {
```
___
___


### Missing checks for address(0x0) when assigning values to address state variables
___
[BlurExchange.sol: L113](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113)
```solidity
        weth = _weth;
```
___
[BlurExchange.sol: L116](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L116)
```solidity
        oracle = _oracle;
```
___
___


### Event is missing `indexed` fields
Each `event` should use three `indexed` fields if there are at least three fields

[BlurExchange.sol: L76-83](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L76-L83)
```solidity
    event OrdersMatched(
        address indexed maker,
        address indexed taker,
        Order sell,
        bytes32 sellHash,
        Order buy,
        bytes32 buyHash
    );
```
___
___


### NatSpec `@return` statements are missing
`@return` statements are missing throughout the contest. Below is one example:

[ExecutionDelegate.sol: L113-122](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L113-L122)
```solidity
     * @dev Transfer ERC20 token
     * @param token address of the token
     * @param from address of the sender
     * @param to address of the recipient
     * @param amount amount
     */
    function transferERC20(address token, address from, address to, uint256 amount)
        approvedContract
        external
        returns (bool)
```
The following functions are also missing `@return` statements but are not missing any other NatSpec:

[ERC1967Proxy.sol: L27-31](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L27-L31)

[PolicyManager.sol: L44-49](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L44-L49)

[PolicyManager.sol: L52-56](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L52-L56)

[PolicyManager.sol: L60-67](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L60-L67)

[BlurExchange.sol: L254-261](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L254-L261)

[BlurExchange.sol: L274-281](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L274-L281)

[BlurExchange.sol: L287-294](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L287-L294)

[BlurExchange.sol: L335-352](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L335-L352)

[BlurExchange.sol: L369-380](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L369-L380)

[BlurExchange.sol: L412-419](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L412-L419)

[BlurExchange.sol: L463-474](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L463-L474)

[BlurExchange.sol: L545-551](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L545-L551)

[MerkleVerifier.sol: L45-52](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L45-L52)
___
___

### NatSpec statements are missing
NetSpec statements are missing for the following functions:

[StandardPolicyERC1155.sol: L9-22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC1155.sol#L9-L22)

[StandardPolicyERC1155.sol: L38-48](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC1155.sol#L38-L48)

[StandardPolicyERC721.sol: L9-22](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC721.sol#L9-L22)

[StandardPolicyERC721.sol: L38-48](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC721.sol#L38-L48)

[EIP712.sol: L39-42](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L39-L42)

[EIP712.sol: L55-58](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L55-L58)

[EIP712.sol: L69-72](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L69-L72)

[EIP712.sol: L84-87](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L84-L87)

[EIP712.sol: L112-115](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L112-L115)

[EIP712.sol: L124-127](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L124-L127)

[EIP712.sol: L139-142](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L139-L142)

[BlurExchange.sol: L94-102](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L94-L102)

[BlurExchange.sol: L215-217](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L215-L217)

[BlurExchange.sol: L224-226](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L224-L226)

[BlurExchange.sol: L233-235](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L233-L235)

[BlurExchange.sol: L242-244](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L242-L244)
___
___


### NatSpec is incomplete
Specific NatSpec statements are missing for the three cases below, as shown:

[ERC1967Proxy.sol: L16-24](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L16-L24)
```solidity
     * @dev Initializes the upgradeable proxy with an initial implementation specified by `_logic`.
     *
     * If `_data` is nonempty, it's used as data in a delegate call to `_logic`. This will typically be an encoded
     * function call, and allows initializating the storage of the proxy like a Solidity constructor.
     */
    constructor(address _logic, bytes memory _data) payable {
        assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));
        _upgradeToAndCall(_logic, _data, false);
    }
```
Missing: `@param _logic` and `@param _data` for `constructor` 
___
[BlurExchange.sol: L396-406](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L396-L406)
```solidity
     * @dev Wrapped ecrecover with safety check for v parameter
     * @param v v
     * @param r r
     * @param s s
     */
    function _recover(
        bytes32 digest,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) internal pure returns (address) {
```
Missing: `@param digest` and `@return`
___
[BlurExchange.sol: L518-531](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L518-L531)
```solidity
     * @dev Execute call through delegate proxy
     * @param collection collection contract address
     * @param from seller address
     * @param to buyer address
     * @param tokenId tokenId
     * @param assetType asset type of the token
     */
    function _executeTokenTransfer(
        address collection,
        address from,
        address to,
        uint256 tokenId,
        uint256 amount,
        AssetType assetType
```
Missing: `@param amount` 
___
___


### Missing `require` revert string
A `require` message should be included to enable users to understand the reason for failure

Recommendation: Add a brief (<= 32 char) explanatory message to each of the three `requires` below. Also, consider using custom error codes (which would be cheaper than revert strings).
___
[BlurExchange.sol: L134](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134)
```solidity
        require(sell.order.side == Side.Sell);
```
___
[BlurExchange.sol: L183](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L183)
```solidity
        require(msg.sender == order.trader);
```
___
[BlurExchange.sol: L452](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L452)
```solidity
            require(msg.value == price);
```
___
___


### Update sensitive terms in both comments and code
Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/).
___
[PolicyManager.sol: L11](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L11)
```solidity
 * @dev Manages the policy whitelist for the Blur exchange
```
Suggestion: Change `whitelist` to `allowlist`. 

Similarly for the remaining instances of `whitelist` and its variants.
___
___
