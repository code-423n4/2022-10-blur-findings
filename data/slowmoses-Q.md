## Possible Overflow

array.length might exceed uint8.max and the code would loop forever and therefore break.

for (uint8 i = 0; i < orders.length; i++) {
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

for (uint8 i = 0; i < fees.length; i++) {
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476

## Inconsistent Variable Names

All names of constant variables should follow the same naming convention. Normal letters or (preferably) capital letters.

string public constant name = "Blur Exchange";
string public constant version = "1.0";
uint256 public constant INVERSE_BASIS_POINT = 10000;
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57-L59

## Compiler Warnings

yarn compile results in a number of warnings. Finished code should compile without warnings:

***

Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/mocks/MockERC1155.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/mocks/MockERC20.sol


Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.
--> contracts/mocks/MockERC721.sol


Warning: Unused local variable.
   --> contracts/BlurExchange.sol:388:14:
    |
388 |             (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
    |              ^^^^^^^^^^^^^^^^^^^^^^^^^^^

## Sensitive Terms

Avoid the use of sensitive terms in favor of neutral ones. Use allowlist rather than whitelist.

Some instances of this issue are listed below.

***

@dev Manages the policy whitelist for the Blur exchange
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L11

EnumerableSet.AddressSet private _whitelistedPolicies;
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L16

event PolicyWhitelisted(address indexed policy);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L19

require(!_whitelistedPolicies.contains(policy), "Already whitelisted");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L26

_whitelistedPolicies.add(policy);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L27

emit PolicyWhitelisted(policy);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L29

require(_whitelistedPolicies.contains(policy), "Not whitelisted");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L37

_whitelistedPolicies.remove(policy);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L38

function isPolicyWhitelisted(address policy) external view override returns (bool) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L47

return _whitelistedPolicies.contains(policy);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L48

@notice View number of whitelisted policies
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L52

function viewCountWhitelistedPolicies() external view override returns (uint256) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L54

return _whitelistedPolicies.length();
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L55

@notice See whitelisted policies
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L59

function viewWhitelistedPolicies(uint256 cursor, uint256 size)
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L63

if (length > _whitelistedPolicies.length() - cursor) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L71

length = _whitelistedPolicies.length() - cursor;
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L72

address[] memory whitelistedPolicies = new address[](length);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L75

whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L78

return (whitelistedPolicies, cursor + length);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/PolicyManager.sol#L81

require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L424

require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L428

## Missing Indexed Fields in Events

Each event should use three indexed fields if there are three or more fields in the event.

***

event OrdersMatched(
    address indexed maker,
    address indexed taker,
    Order sell,
    bytes32 sellHash,
    Order buy,
    bytes32 buyHash
);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L76-L83

## Avoid Magic Numbers

Checking against magic numbers should be avoided.
Consider using a properly named constant instead.

***

require(v == 27 || v == 28, "Invalid v parameter");
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L407

## Revert and Require Should Have Descriptive Reason Strings

require(sell.order.side == Side.Sell);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L134

require(msg.sender == order.trader);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L183

require(msg.value == price);
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L452

## Avoid Overlong Lines

Limit line length to 164 characters.
Longer lines will need scrollbars in github and are hard to read anyways.

***

        "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L24

        "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"

https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L27

## Missing NatSpec

Every function should be documented, every parameter should be documented, and every return value should be documented.

***

Named return value not documented in a machine-readable way: impl

    /**
    * @dev Returns the current implementation address.
    */
    
    function _implementation() internal view virtual override returns (address impl) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/ERC1967Proxy.sol#L25-L29

***

Undocumented function:

function canMatchMakerAsk(Order calldata makerAsk, Order calldata takerBid)
external
pure
override
returns (
bool,
uint256,
uint256,
uint256,
AssetType
)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/matchingPolicies/StandardPolicyERC1155.sol#L12-L23

***

Undocumented function:

function canMatchMakerBid(Order calldata makerBid, Order calldata takerAsk)
external
pure
override
returns (
bool,
uint256,
uint256,
uint256,
AssetType
)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/matchingPolicies/StandardPolicyERC1155.sol#L38-L49

***

Undocumented function:

function canMatchMakerAsk(Order calldata makerAsk, Order calldata takerBid)
external
pure
override
returns (
bool,
uint256,
uint256,
uint256,
AssetType
)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/matchingPolicies/StandardPolicyERC721.sol#L12-L23

***

Undocumented function:

function canMatchMakerBid(Order calldata makerBid, Order calldata takerAsk)
external
pure
override
returns (
bool,
uint256,
uint256,
uint256,
AssetType
)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/matchingPolicies/StandardPolicyERC721.sol#L38-L49

***

Undocumented return value: bool

    /**
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
    {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L111-L123

***

Undocumented function:

function _hashDomain(EIP712Domain memory eip712Domain)
internal
pure
returns (bytes32)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L39-L43

***

Undocumented function:

function _hashFee(Fee calldata fee)
internal
pure
returns (bytes32)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L55-L59

***

Undocumented function:

function _packFees(Fee[] calldata fees)
internal
pure
returns (bytes32)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L69-L73

***

Undocumented function:

function _hashOrder(Order calldata order, uint256 nonce)
internal
pure
returns (bytes32)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L84-L88

***

Undocumented function:

function _hashToSign(bytes32 orderHash)
internal
view
returns (bytes32 hash)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L112-L116

***

Undocumented function:

function _hashToSignRoot(bytes32 root)
internal
view
returns (bytes32 hash)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L124-L128

***

Undocumented function:

function _hashToSignOracle(bytes32 orderHash, uint256 blockNumber)
internal
view
returns (bytes32 hash)
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/EIP712.sol#L139-L143

***

Undocumented function:

function open() external onlyOwner {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L43-L43

***

Undocumented function:

function close() external onlyOwner {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L47-L47

***

Undocumented function:

function _authorizeUpgrade(address) internal override onlyOwner {}
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L53-L53

***

Undocumented function:

function initialize(
uint chainId,
address _weth,
IExecutionDelegate _executionDelegate,
IPolicyManager _policyManager,
address _oracle,
uint _blockRange
) public initializer {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L95-L102

***

Undocumented function:

function setExecutionDelegate(IExecutionDelegate _executionDelegate)
external
onlyOwner
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L215-L218

***

Undocumented function:

function setPolicyManager(IPolicyManager _policyManager)
external
onlyOwner
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L224-L227

***

Undocumented function:

function setOracle(address _oracle)
external
onlyOwner
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L233-L236

***

Undocumented function:

function setBlockRange(uint256 _blockRange)
external
onlyOwner
{
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L242-L245

***

Undocumented parameter: digest

    /**
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
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L394-L406

***

Undocumented return value: uint256

    /**
    * @dev Charge a fee in ETH or WETH
    * @param fees fees to distribute
    * @param paymentToken address of token to pay in
    * @param from address to charge fees
    * @param price price of token
    */
    function _transferFees(
    Fee[] calldata fees,
    address paymentToken,
    address from,
    uint256 price
    ) internal returns (uint256) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L461-L474

***

Undocumented parameter: amount

    /**
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
    ) internal {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L516-L532

***

Undocumented function:

function _hashPair(bytes32 a, bytes32 b) private pure returns (bytes32) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L45-L45

***

Undocumented function:

function _efficientHash(
bytes32 a,
bytes32 b
) private pure returns (bytes32 value) {
https://github.com/code-423n4/2022-10-blur/tree/main/contracts/lib/MerkleVerifier.sol#L49-L52

