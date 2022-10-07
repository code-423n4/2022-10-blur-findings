# Unsued Return
```PolicyManager.addPolicy(address)``` and ```PolicyManager.removePolicy(address)``` ignore the return of ```_whitelistedPolicies.add``` and ```_whitelistedPolicies.remove```respectively.
### Impact
The return value of an external call is not stored in a local or state variable. The lack of return could cause unexpected behavior of the contract in the future.
### Location
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L27
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L38
### Solution
Consider using the boolean return to validate if the operation went as expected.
# Lack of zero check address
It's possivle to set an 0x0 address to an admin role.
### Impact
If, by mistake, an empty address (0x0) is set as 'executionDelegate', for example, the contract's functions could be compromised.
### Location
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L113-L117
### Solution
```
require(admin != (0x0), "");
```
# Unsafe ERC20 operations
Contracts are using ```.transfer()``` and ```.transferFrom()```.
### Impact
ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.
It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.
To circumvent ERC20's approve functions race-condition vulnerability use OpenZeppelin's SafeERC20 library's safe{Increase|Decrease}Allowance functions.
In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.
### Location
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L508
```
        if (paymentToken == address(0)) {
            /* Transfer funds in ETH. */
            payable(to).transfer(amount);
        } else if (paymentToken == weth) {
            /* Transfer funds in WETH. */
            executionDelegate.transferERC20(weth, from, to, amount);
        } else {
            revert("Invalid payment token");
        }
    }
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L125
```
    function transferERC20(address token, address from, address to, uint256 amount)
        approvedContract
        external
        returns (bool)
    {
        require(revokedApproval[from] == false, "User has revoked approval");
        return IERC20(token).transferFrom(from, to, amount);
    }
}
```
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L78
```
    function transferERC721Unsafe(address collection, address from, address to, uint256 tokenId)
        approvedContract
        external
    {
        require(revokedApproval[from] == false, "User has revoked approval");
        IERC721(collection).transferFrom(from, to, tokenId);
    }
```
### Solution
The better solution is to use ```safeTrasferFrom()``` and ```safeTranfer()```


