## [L-01] BUGS IN SOLIDITY 0.8.13
Solidity 0.8.13 is currently in use. However, it does have a size check bug involving nested calldata array and `abi.encode` and an optimizer bug affecting inline assembly. It looks like that the code in scope are not affected by these bugs but the protocol team should be aware of them.

Please see the following links for reference:
- https://blog.soliditylang.org/2022/05/17/calldata-reencode-size-check-bug/
- https://blog.soliditylang.org/2022/06/15/inline-assembly-memory-side-effects-bug/

To be more future-proofed, please consider using at least Solidity 0.8.15 instead of 0.8.13.

## [L-02] `StandardPolicyERC1155` CONTRACT COULD SUPPORT TRANSFERRING MULTIPLE TOKENS OF SAME `tokenId` AT ONCE
Due to the hardcoded 1 in the following `canMatchMakerAsk` and `canMatchMakerBid` functions, the `StandardPolicyERC1155` contract currently only supports transferring one token at a time for the same `tokenId`. However, there are use cases that users want to transfer multiple tokens of the same `tokenId` at once, such as for fractional NFTs. For these use cases, users currently must create many orders in which each uses this `StandardPolicyERC1155` contract as its matching policy. This is very inconvenient and can cost much gas. Creating and executing many orders can even be impossible if the user needs to transfer thousands of the tokens for the same `tokenId` in which such a transaction can require gas that exceeds the block gas limit. As a result, the usability of the system becomes limited, and the user experience becomes degraded.

To address this issue, in the `StandardPolicyERC1155` contract, please consider updating the `canMatchMakerAsk` function to  additionally checking `(makerAsk.amount == takerBid.amount)` for the first `bool` returned variable and return `makerAsk`'s `amount` instead of 1 for the last `uint256` returned variable; the `canMatchMakerBid` function can be changed similarly as well.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC1155.sol#L12-L36
```solidity
    function canMatchMakerAsk(Order calldata makerAsk, Order calldata takerBid)
        ...
    {
        return (
            (makerAsk.side != takerBid.side) &&
            (makerAsk.paymentToken == takerBid.paymentToken) &&
            (makerAsk.collection == takerBid.collection) &&
            (makerAsk.tokenId == takerBid.tokenId) &&
            (makerAsk.matchingPolicy == takerBid.matchingPolicy) &&
            (makerAsk.price == takerBid.price),
            makerAsk.price,
            makerAsk.tokenId,
            1,
            AssetType.ERC1155
        );
    }
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/matchingPolicies/StandardPolicyERC1155.sol#L38-L62
```solidity
    function canMatchMakerBid(Order calldata makerBid, Order calldata takerAsk)
        ...
    {
        return (
            (makerBid.side != takerAsk.side) &&
            (makerBid.paymentToken == takerAsk.paymentToken) &&
            (makerBid.collection == takerAsk.collection) &&
            (makerBid.tokenId == takerAsk.tokenId) &&
            (makerBid.matchingPolicy == takerAsk.matchingPolicy) &&
            (makerBid.price == takerAsk.price),
            makerBid.price,
            makerBid.tokenId,
            1,
            AssetType.ERC1155
        );
    }
```

## [L-03] `_blockRange` INPUT CAN BE CHECKED FOR HAVING A SENSIBLE VALUE IN `setBlockRange` FUNCTION
Because of `require(block.number - order.blockNumber < blockRange, "Signed block number out of range")` in the `_validateSignatures` function, setting `blockRange` to 0 by calling the `setBlockRange` function will make all orders with 0 `expirationTime` not working. Setting `blockRange` to 1 requires calling the `execute` function in the same block when such order is added in the order book, which however could violate the `listingTime < block.timestamp` condition in the `_canSettleOrder` function. To ensure that the orders with 0 `expirationTime` can work properly, please consider disallowing the `_blockRange` input from being smaller than X in the `setBlockRange` function, where X is the smallest block range value that is sensible.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L242-L248
```solidity
    function setBlockRange(uint256 _blockRange)
        external
        onlyOwner
    {
        blockRange = _blockRange;
        emit NewBlockRange(blockRange);
    }
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L291-L332
```solidity
    function _validateSignatures(Input calldata order, bytes32 orderHash)
        internal
        view
        returns (bool)
    {

        ...

        if (order.order.expirationTime == 0) {
            /* Check oracle authorization. */
            require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
            ...
        }

        return true;
    }
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L278-L284
```solidity
    function _canSettleOrder(uint256 listingTime, uint256 expirationTime)
        view
        internal
        returns (bool)
    {
        return (listingTime < block.timestamp) && (expirationTime == 0 || block.timestamp < expirationTime);
    }
```

## [L-04] MISSING `address(0)` CHECKS FOR CRITICAL ADDRESS INPUTS
To prevent unintended behaviors, critical address inputs should be checked against `address(0)`.

Please consider checking `_weth` and `_oracle` in the following `initialize` function.
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95-L118
```solidity
    function initialize(
        uint chainId,
        address _weth,
        IExecutionDelegate _executionDelegate,
        IPolicyManager _policyManager,
        address _oracle,
        uint _blockRange
    ) public initializer {
        ...

        weth = _weth;
        ...
        oracle = _oracle;
        ...
    }
```

## [N-01] MISSING INDEXED EVENT FIELD
Querying event can be optimized with index. Please consider adding `indexed` to the relevant field of the following event.

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L86
```solidity
    event NonceIncremented(address trader, uint256 newNonce);
```

## [N-02] MISSING REASON STRINGS IN `require` STATEMENTS
When the reason strings are missing in the `require` statements, it is unclear about why certain conditions revert. Please add descriptive reason strings for the following `require` statements.
```solidity
contracts\BlurExchange.sol
  134: require(sell.order.side == Side.Sell);
  183: require(msg.sender == order.trader);
  452: require(msg.value == price);
```

## [N-03] `uint256` CAN BE USED INSTEAD OF `uint`
For explicitness and consistency, `uint256` can be used instead `uint` for the following code.
```solidity
contracts\BlurExchange.sol
  96: uint chainId,
  101: uint _blockRange
  553: uint size;
```

## [N-04] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. @param and/or @return comments are missing for the following functions. Please consider completing NatSpec comments for them.
```solidity
contracts\BlurExchange.sol
  258: function _validateOrderParameters(Order calldata order, bytes32 orderHash)  
  278: function _canSettleOrder(uint256 listingTime, uint256 expirationTime)  
  291: function _validateSignatures(Input calldata order, bytes32 orderHash)  
  344: function _validateUserAuthorization(  
  375: function _validateOracleAuthorization(  
  401: function _recover(  
  416: function _canMatchOrders(Order calldata sell, Order calldata buy)   
  469: function _transferFees( 
  548: function _exists(address what)  

contracts\ExecutionDelegate.sol
  119: function transferERC20(address token, address from, address to, uint256 amount) 

contracts\PolicyManager.sol
  47: function isPolicyWhitelisted(address policy) external view override returns (bool) {    

contracts\lib\ERC1967Proxy.sol
  29: function _implementation() internal view virtual override returns (address impl) {  

contracts\lib\MerkleVerifier.sol
  33: function _computeRoot(  
```

## [N-05] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.
```solidity
contracts\BlurExchange.sol
  43: function open() external onlyOwner {    
  47: function close() external onlyOwner {    
  95: function initialize(    
  215: function setExecutionDelegate(IExecutionDelegate _executionDelegate) 
  224: function setPolicyManager(IPolicyManager _policyManager) 
  233: function setOracle(address _oracle) 
  242: function setBlockRange(uint256 _blockRange) 

contracts\lib\EIP712.sol
  39: function _hashDomain(EIP712Domain memory eip712Domain)  
  55: function _hashFee(Fee calldata fee) 
  69: function _packFees(Fee[] calldata fees) 
  84: function _hashOrder(Order calldata order, uint256 nonce)    
  112: function _hashToSign(bytes32 orderHash)    
  124: function _hashToSignRoot(bytes32 root)    
  139: function _hashToSignOracle(bytes32 orderHash, uint256 blockNumber)    

contracts\lib\MerkleVerifier.sol
  45: function _hashPair(bytes32 a, bytes32 b) private pure returns (bytes32) {   
  49: function _efficientHash(   

contracts\matchingPolicies\StandardPolicyERC721.sol
  12: function canMatchMakerAsk(Order calldata makerAsk, Order calldata takerBid) 
  38: function canMatchMakerBid(Order calldata makerBid, Order calldata takerAsk) 

contracts\matchingPolicies\StandardPolicyERC1155.sol
  12: function canMatchMakerAsk(Order calldata makerAsk, Order calldata takerBid) 
  38: function canMatchMakerBid(Order calldata makerBid, Order calldata takerAsk) 
```