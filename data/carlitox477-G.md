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


# BlurExchange#_exists: used just one can be writen inline to save gas
```exists``` is just used by function ```_executeTokenTransfer```.

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.


# Use custom errors rather than revert()/require() strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [~50](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) gas each time they’re hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas. Instances found:
* [ReentrancyGuarded#reentrancyGuard](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L14)
* [PolicyManager#addPolicy](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L26)
* [PolicyManager#removePolicy](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L37)
* [ExecutionDelegate#approvedContract](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L22)
* [ExecutionDelegate#transferERC721Unsafe](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L77)
* [ExecutionDelegate#transferERC721](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L92)
* [ExecutionDelegate#transferERC1155](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L108)
* [ExecutionDelegate#transferERC20](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L124)
* [BlurExchange#execute](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L139-L143)
* [BlurExchange#setExecutionDelegate](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L219)
* [BlurExchange#setPolicyManager](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L228)
* [BlurExchange#setOracle](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L237)
* [BlurExchange#_validateSignatures](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L318)
* [BlurExchange#_recover](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L407)
* [BlurExchange#_canMatchOrders 1](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L424)
* [BlurExchange#_canMatchOrders 2](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L428)
* [BlurExchange#_canMatchOrders 3](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L431)
* [BlurExchange#_transferFees](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L482)
* [BlurExchange#_transferTo](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L513)
* [BlurExchange#_executeTokenTransfer](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L534)

# Using private rather than public for constant saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table.
* Change [EIP712 constants](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L20-L35) visibility to private 
* Change [BlurExchange constants](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57-L59) visibility to private 

Create a single getter to get all the constant for each contract.

# <array>.length should not be looked up in every loop of a for-loop
The overheads outlined below are PER LOOP, excluding the first loop
* storage arrays incur a Gwarmaccess (100 gas)
* memory arrays use ```MLOAD``` (3 gas)
* calldata arrays use ```CALLDATALOAD``` (3 gas)
Caching the length changes each of these to a ```DUP<N>``` (3 gas), and gets rid of the extra ```DUP<N>``` needed to store the stack offset.

Instances found:
* [PolicyManager#viewWhitelistedPolicies](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77)
* [BlurExchange#cancelOrders](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199)
* [BlurExchange#_transferFees](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476)
* [MerkleVerifier #_computeRoot](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38)
