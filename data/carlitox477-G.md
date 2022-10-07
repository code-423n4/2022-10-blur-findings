# StandardPolicyERC1155#canMatchMakerAsk and StandardPolicyERC1155#canMatchMakerBid do the same, code can be reduced

The implementation can be reduced to:
```solidity
contract StandardPolicyERC1155 is IMatchingPolicy {
    function _canMatchMakerBidOrAsk(Order calldata makerAskOrBid, Order calldata takerBidOrAsk)
        internal
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
        return (
            (makerAskOrBid.side != takerBidOrAsk.side) &&
            (makerAskOrBid.paymentToken == takerBidOrAsk.paymentToken) &&
            (makerAskOrBid.collection == takerBidOrAsk.collection) &&
            (makerAskOrBid.tokenId == takerBidOrAsk.tokenId) &&
            (makerAskOrBid.matchingPolicy == takerBidOrAsk.matchingPolicy) &&
            (makerAskOrBid.price == takerBidOrAsk.price),
            makerAskOrBid.price,
            makerAskOrBid.tokenId,
            1,
            AssetType.ERC1155
        );
    }

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
        return _canMatchMakerBidOrAsk(makerAsk, takerBid)
    }

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
        return _canMatchMakerBidOrAsk(makerBid, takerAsk) 
    }
}
```

# StandardPolicyERC721.sol#canMatchMakerAsk and StandardPolicyERC721.sol#canMatchMakerBid do the same, code can be reduced
The implementation can be reduced to:
```
contract StandardPolicyERC721 is IMatchingPolicy {
    function _canMatchMakerAskOrBid(Order calldata makerAskOrBid, Order calldata takerAskOrBid)
        internal
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
        return (
            (makerAskOrBid.side != takerAskOrBid.side) &&
            (makerAskOrBid.paymentToken == takerAskOrBid.paymentToken) &&
            (makerAskOrBid.collection == takerAskOrBid.collection) &&
            (makerAskOrBid.tokenId == takerAskOrBid.tokenId) &&
            (makerAskOrBid.matchingPolicy == takerAskOrBid.matchingPolicy) &&
            (makerAskOrBid.price == takerAskOrBid.price),
            makerAskOrBid.price,
            makerAskOrBid.tokenId,
            1,
            AssetType.ERC721
        );
    }
    
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
        return _canMatchMakerAskOrBid(makerAsk,takerBid)
    }

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
        return _canMatchMakerAskOrBid(makerBid,takerAsk)
    }
}
```


# i++ should be replaced with unchecked{++i}
The option suggested consume less gas:
* [MerkleVerifier#_computeRoot](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
* [PolicyManager#viewWhitelistedPolicies](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)
* [PolicyManager#cancelOrders](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199) and [PolicyManager#_transferFees](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476). In these cases, is important to verify that ```orders``` and ```fees``` length are less than 2^8

# BlurExchange: Avoid state variable access by using calldata
* [BlurExchange#setExecutionDelegate](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L221): Replace it for ```emit NewExecutionDelegate(_executionDelegate);```
* [BlurExchange#setPolicyManager](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L230): Replace it for ```emit NewPolicyManager(_policyManager);```
* [BlurExchange#setOracle](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L239): Replace it for ```emit NewOracle(_oracle);```
* [BlurExchange#setBlockRange](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L247): Replace it for ```emit NewBlockRange(_blockRange);```


# BlurExchange: weth can be decleared as immutable and set in the constructor
This will save gas avoid access it as a storage value:

```solidity
    address public immutable weth; //Replaces address public weth;
    
    constructor(address _weth) initializer{
        weth = weth
    }
    /* Constructor (for ERC1967) */
    function initialize(
        uint chainId,
        address _weth,
        IExecutionDelegate _executionDelegate,
        IPolicyManager _policyManager,
        address _oracle,
        uint _blockRange
    ) public initializer {
        __Ownable_init();
        isOpen = 1;

        DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({
            name              : name,
            version           : version,
            chainId           : chainId,
            verifyingContract : address(this)
        }));

        //weth = _weth; THIS IS ERASED
        executionDelegate = _executionDelegate;
        policyManager = _policyManager;
        oracle = _oracle;
        blockRange = _blockRange;
    }
```