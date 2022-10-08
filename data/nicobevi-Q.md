1. LOW - Replace `isOpen` functionality with `Pausable` Openzeppelin's standard contract.

The functionality below can be removed and replaced with `@openzeppelin-contracts-upgradeable/security/PausableUpgradeable.sol`.

```solidity
  /* Auth */
    uint256 public isOpen; // @audit QA: use paused openzeppling contract instead of custom functionality

    modifier whenOpen() {
        require(isOpen == 1, "Closed");
        _;
    }

    event Opened();
    event Closed();

    function open() external onlyOwner {
        isOpen = 1;
        emit Opened();
    }
    function close() external onlyOwner {
        isOpen = 0;
        emit Closed();
    }
```

2. LOW - Replace `ReentrancyGuarded.sol` contract with `@openzeppelin-contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol`

Contract `./lib/ReentrancyGuarded.sol` can be completely replaced with Openzeppelin's standard library.

3. QA - Use `1e4` notation on `INVERSE_BASIS_POINT`.

Link: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59

Replace `10000` with `1e4` or `10_000` to improbe readability.

4. LOW - Validate `_weth`, `_oracle`, `_executionDelegate` and `_policyManager` addresses are not `address(0)` on initialization.

Link: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L94

Reason: An invalid configuration of `_weth`, `_oracle`, `_executionDelegate` or `_policyManager` addresses could cause unexpected behaviours and break the contract functionality.

Recomendation: Add these validations to initialize body
```solidity
require(_weth != address(0), "Invalid _weth");
require(_oracle != address(0), "Invalid _oracle");
require(address(_executionDelegate) != address(0), "Invalid _executionDelegate");
require(address(_policyManager) != address(0), "Invalid _policyManager");
```

6. QA - Missing error message on `execute()` require validation.

Link: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L133

Replace L133 

```solidity
require(sell.order.side == Side.Sell);
```

with:

```solidity
require(sell.order.side == Side.Sell, "Invalid order side");
```

7. QA - Move `cancellOrFilled` set above execution functions are called.

`Checks, Effects, Interactions` pattern is recommended to avoid reentrancy attacks. Orders must be marked as filled before tokens are transfered.

Lines: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol/#L146-L165

Change:

```solidity
_executeFundsTransfer(
    sell.order.trader,
    buy.order.trader,
    sell.order.paymentToken,
    sell.order.fees,
    price
);
_executeTokenTransfer(
    sell.order.collection,
    sell.order.trader,
    buy.order.trader,
    tokenId,
    amount,
    assetType
);

/* Mark orders as filled. */ 
cancelledOrFilled[sellHash] = true;
cancelledOrFilled[buyHash] = true; 
```

to: 

```solidity
/* Mark orders as filled. */ 
cancelledOrFilled[sellHash] = true;
cancelledOrFilled[buyHash] = true; 

_executeFundsTransfer(
    sell.order.trader,
    buy.order.trader,
    sell.order.paymentToken,
    sell.order.fees,
    price
);
_executeTokenTransfer(
    sell.order.collection,
    sell.order.trader,
    buy.order.trader,
    tokenId,
    amount,
    assetType
);
```

8. QA - Missing error message on `cancelOrder()`

Line: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol/#L189

Replace L189:

```solidity
require(msg.sender == order.trader);
```

with:

```solidity
require(msg.sender == order.trader, "Sender is not trader");
```

9. QA - Invalid `@natspec` `@dev` comment on `incrementNonce()`

Line: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol/#L212

Comment is invalid, replace it with a valid description of the function or remove it completly.

10. QA - Remove `== false` with 

Line: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol/#L274

Replace

```solidity
(cancelledOrFilled[orderHash] == false) &&
```

with

```solidity
!cancelledOrFilled[orderHash] &&
```


11. QA - Replace parameter name `order` to `input` on `_validateSignatures`

Lines: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol/#L291-L332

Parameter name can be confusing and could result in errors. We recommend to change the name to `input`.

```solidity
function _validateSignatures(Input calldata order, bytes32 orderHash)
    internal
    view
    returns (bool)
{


    if (order.order.trader == msg.sender) {
      return true;
    }


    /* Check user authorization. */
    if (
        !_validateUserAuthorization(
            orderHash,
            order.order.trader,
            order.v,
            order.r,
            order.s,
            order.signatureVersion,
            order.extraSignature
        )
    ) {
        return false;
    }


    if (order.order.expirationTime == 0) {
        /* Check oracle authorization. */
        require(block.number - order.blockNumber < blockRange, "Signed block number out of range");
        if (
            !_validateOracleAuthorization(
                orderHash,
                order.signatureVersion,
                order.extraSignature,
                order.blockNumber
            )
        ) {
            return false;
        }
    }


    return true;
}
```

to

```solidity
function _validateSignatures(Input calldata input, bytes32 orderHash)
    internal
    view
    returns (bool)
{


    if (input.order.trader == msg.sender) {
      return true;
    }


    /* Check user authorization. */
    if (
        !_validateUserAuthorization(
            orderHash,
            input.order.trader,
            input.v,
            input.r,
            input.s,
            input.signatureVersion,
            input.extraSignature
        )
    ) {
        return false;
    }


    if (input.order.expirationTime == 0) {
        /* Check oracle authorization. */
        require(block.number - input.blockNumber < blockRange, "Signed block number out of range");
        if (
            !_validateOracleAuthorization(
                orderHash,
                input.signatureVersion,
                input.extraSignature,
                input.blockNumber
            )
        ) {
            return false;
        }
    }


    return true;
}
```

12. QA - Replace `_exists(address)` function and use `isContract()` on `@openzeppelin-contracts/utils/Address.sol`.

Lines: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol/#L548-L558

Duplicated functionality that can be found in Openzeppelin`s library. Also the function name and comment are not accurate. What it's trying to verify is that the address provided is a contract, not that the address exists.

13. LOW - `MerkleVerifier.sol` can be replaced with `@openzeppelin-contracts/utils/cryptography/MerkleProof.sol`.

`./lib/MerkleVerifier.sol` contract contains duplicated functionality that can be completly replaced using the standard and audited Openzeppelin`s library.

14. QA - Change `contracts` variable name to `approvedContracts` on `ExecutionDelegate.sol`.

The variable name does not provide enough information about its content.

Line: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol/#L18

15. QA - Merge `approveContract(address _contract)` and `denyContract(address _contract)` in a single `changeContractApprove(address _contract, bool _approved)` function to save gas and reduce contract lines..

Lines: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol/#L36-L48

Replace 

```solidity
/**
  * @dev Approve contract to call transfer functions
  * @param _contract address of contract to approve
  */
function approveContract(address _contract) onlyOwner external {    
    contracts[_contract] = true;
    emit ApproveContract(_contract);
}

/**
  * @dev Revoke approval of contract to call transfer functions
  * @param _contract address of contract to revoke approval
  */
function denyContract(address _contract) onlyOwner external {
    contracts[_contract] = false;
    emit DenyContract(_contract);
}
```

With:

```solidity
/**
  * @dev Approve or deny a contract to call transfer functions
  * @param _contract address of contract to approve
  */
function changeContractApprove(address _contract, bool _approved) onlyOwner external {    
    contracts[_contract] = _approved;
    emit ContractApproveChanged(_contract, _approved);
}
```

16 - QA - Merg `revokeApproval()` and `grantApproval()` in a single `changeRevokedApproval(bool _approved)` function to save gas and reduce contract lines.

Lines: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol/#L50-L64

Change:

```solidity
/**
  * @dev Block contract from making transfers on-behalf of a specific user
  */
function revokeApproval() external {
    revokedApproval[msg.sender] = true;
    emit RevokeApproval(msg.sender);
}

/**
  * @dev Allow contract to make transfers on-behalf of a specific user
  */
function grantApproval() external {
    revokedApproval[msg.sender] = false;
    emit GrantApproval(msg.sender);
}
```

With

```solidity
/**
  * @dev Allow contract to make transfers on-behalf of a specific user
  */
function changeRevokedApproval(bool _revoked) external {
    revokedApproval[msg.sender] = _revoked;
    emit RevokedApprovalChanged(msg.sender, _revoked);
}
```

17 - LOW - Remove unused and unsafe `transferERC721Unsafe`.

Lines: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol/#L66-L79

This function is not being used and could be a potencial security risk. We recommend to remove it completly.

18 - QA - Change `== false` on `transferERC1155()`

Line: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol/#L92

Change

```solidity
require(revokedApproval[from] == false, "User has revoked approval");
```

With

```solidity
require(!revokedApproval[from], "User has revoked approval");
```

19 - LOW - Change `IERC20.transferFrom` call to `IERC20.safeTransferFrom()`.

Line: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol/#L125

Replace it with

```solidity
IERC20(token).safeTransferFrom(from, to, amount);
```

20 - QA - Remove returned bool on `transferERC20()`.

Line: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol/#L122

Using `safeTransferFrom` as we recommended above, we recommend to remove the returned boolean on this function.

21 - QA - `ERC1967Proxy.sol` Can be removed completely and use `@openzeppelin/contracts/proxy/ERC1967/ERC1967Upgrade.sol` instead.

There is no extra functionality added on this contract, thus, be recommend to remove it and use Openzeppelin's contract instead.

22 - QA - Upgrade Openzeppelin's libraries to last version:

`@openzeppelin/contracts` can be upgraded from `v4.4.1` to `v4.7.3`.
`@openzeppelin/contracts-upgradeable` can be upgraded from  `v4.6.0` to `v4.8.0-rc.1`.

23 - QA - Some `./lib/EIP712.sol` functionality can be removed extending `@openzeppelin/contracts-upgradeable/utils/cryptography/EIP721Upgradeable.sol`

`./lib/EIP712.sol` should extends `EIP721Upgradeable.sol` contract adding the necessary extra functionality but using what was already made on the library.

24 - Remove unnecessary `pragma abicoder v2;`

abicoder v2 is enabled by default on solidity 0.8. Thus, this pragma definition is not needed.

Lines: 
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L3
* https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L3

25 - Remove unused variable `merklePath` and unnecessary variables `_v`, `_r` and `_s`

Lines: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388-L389

Change this lines:
```solidity
(bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
v = _v; r = _r; s = _s;
```

to

```solidity
(, v, r, s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
```

