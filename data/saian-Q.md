
# Low severity findings

## Malicious owner can steal assets

Users approve `ExecutionDelegate` contract for transferring assets when order is filled. A malicious owner can approve arbitrary contract/EOA to transfer assets out of the contract.
Consider removing the function and storing the exchange address in the constructor, or use a multi-sig contract as the owner address

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L36

```
    function approveContract(address _contract) onlyOwner external { 
        contracts[_contract] = true;
        emit ApproveContract(_contract);
    }
```

## `approveContract` can approve EOAs

`approveContract` is used to approve contracts to transfer assets out of the contract. But since no `isContract` exists, even EOAs can be approved to transfer assets

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L36

```
    function approveContract(address _contract) onlyOwner external { 
        contracts[_contract] = true;
        emit ApproveContract(_contract);
    }
```

Add `isContract` function to check if the address is a contract

```
    function approveContract(address _contract) onlyOwner external { 
        require(isContract(_contract), "...");
        contracts[_contract] = true;
        emit ApproveContract(_contract);
    }
```

## `_blockrange` is not validated

In `setBlockRange` the `_blockRange` value is not validated, so it can be set to 0, preventing oracle authorisation, or set to a very high value, allowing old signatures/ Add input validation to validate the blockrange value

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L246

```
    function setBlockRange(uint256 _blockRange)
        external
        onlyOwner
    {
        blockRange = _blockRange;
        emit NewBlockRange(blockRange); 
    }
```

## If payment token is WETH, msg.value should be 0

Since execute can receive both ether and WETH, checks should be added to prevent eth transfer when the payment token is WETH to prevent ether from being locked in the contract
Add function to retrived ether or accidentally sent ERC20 tokens from the contract

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L451

```
        if (paymentToken == address(0)) {
            require(msg.value == price);    
        }
```

checks should be added to prevent ether transfer if payment token is WETH
Add function to retreive ether or accidentally sent ERC20 tokens

```
        if (paymentToken == address(0)) {
            require(msg.value == price,"...");    
        } else {
            require(msg.value == 0,"...")
        }
```

## Use `call()` instead of `transfer()`

Since transfer forwards a fixed amount of gas 2300, the calls may fail if gas costs change in the future, or if the receiver is a smart contract that consumes more than 2300 gas.
Use `.call()` to send ether instead of transfer.

refer-https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L508

```
    payable(to).transfer(amount);
```

# Non-critical findings

## Missing comments

missing `@amount` param in

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L523

missing `@returns` param in 

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L31

```
    /**
     * @dev Compute the merkle root
     * @param leaf leaf
     * @param proof proof
     */
    function _computeRoot(
```

missing `@returns` in

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L117

```
    /**
     * @dev Transfer ERC20 token
     * @param token address of the token
     * @param from address of the sender
     * @param to address of the recipient   
     * @param amount amount
     */
    function transferERC20(address token, address from, address to, uint256 amount)
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L45

```
    /**
     * @notice Returns if a policy has been added
     * @param policy address of the policy to check 
     */
    function isPolicyWhitelisted(address policy) external view override returns (bool)
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L52

```
    /**
     * @notice View number of whitelisted policies   
     */
    function viewCountWhitelistedPolicies() external view override returns (uint256)

```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L61

```
    /**
     * @notice See whitelisted policies 
     * @param cursor cursor
     * @param size size
     */
    function viewWhitelistedPolicies(uint256 cursor, uint256 size)
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L546

```
    /**
     * @dev Determine if the given address exists
     * @param what address to check
     */
    function _exists(address what) 
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L467

```
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
    ) internal returns (uint256)
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L414

```
    /**
     * @dev Call the matching policy to check orders can be matched and get execution parameters
     * @param sell sell order
     * @param buy buy order
     */
    function _canMatchOrders(Order calldata sell, Order calldata buy)
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L405

```    
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
    ) internal pure returns (address)
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L373

```
    /**
     * @dev Verify the validity of oracle signature
     * @param orderHash hash of the order
     * @param signatureVersion signature version
     * @param extraSignature packed oracle signature
     * @param blockNumber block number used in oracle signature
     */
    function _validateOracleAuthorization
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L342

```
    /**
     * @dev Verify the validity of the user signature
     * @param orderHash hash of the order
     * @param trader order trader who should be the signer
     * @param v v
     * @param r r
     * @param s s
     * @param signatureVersion signature version
     * @param extraSignature packed merkle path
     */
    function _validateUserAuthorization(
        bytes32 orderHash,
        address trader,
        uint8 v,
        bytes32 r,
        bytes32 s,
        SignatureVersion signatureVersion,
        bytes calldata extraSignature
    ) internal view returns (bool)
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L289

```
    /**
     * @dev Verify the validity of the signatures
     * @param order order
     * @param orderHash hash of order
     */
    function _validateSignatures(Input calldata order, bytes32 orderHash)
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L256

```

    /**
     * @dev Check if the order can be settled at the current timestamp
     * @param listingTime order listing time
     * @param expirationTime order expiration time
     */
    function _canSettleOrder(uint256 listingTime, uint256 expirationTime)
```
## Unused import

Files imported are not used

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L5

```
    import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L8

```
    import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
```

## Events not indexed

Events should use indexed fields, indexed fields help off-chain scripts to efficiently filter these events

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L85-L91

```
    event OrderCancelled(bytes32 hash);
    event NonceIncremented(address trader, uint256 newNonce);   

    event NewExecutionDelegate(IExecutionDelegate executionDelegate);
    event NewPolicyManager(IPolicyManager policyManager);
    event NewOracle(address oracle);
    event NewBlockRange(uint256 blockRange);

```
## Missing revert messages

Require statements does not have revert messages, So if the require condition fails, the transaction will revert without providing informative messages to the user. 

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L134

```
    require(sell.order.side == Side.Sell); 
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L452

```
    require(msg.value == price); 
```

## abicoder v2 is used by default from 0.8.0

abicoder is used by default from solidity version 0.8.0

https://blog.soliditylang.org/2020/12/16/solidity-v0.8.0-release-announcement/