## Clearer variable name

In the file BlurExchange.sol between the lines 544-560 is the word "what" used as variable name. https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L544

```
    /**
     * @dev Determine if the given address exists
     * @param what address to check
     */
    function _exists(address what)
        internal
        view
        returns (bool)
    {
        uint size;
        assembly {
            size := extcodesize(what)
        }
        return size > 0;
    }
```
The word "what" doesn't give a very good indication of the usage. So you can better use a function name like addressToCheck

```
    /**
     * @dev Determine if the given address exists
     * @param addressToCheck address to check
     */
    function _exists(address addressToCheck)
        internal
        view
        returns (bool)
    {
        uint size;
        assembly {
            size := extcodesize(addressToCheck)
        }
        return size > 0;
    }
}
```


---------------------------------------------------------------------


## Checking variable against a constant bool

in all these lines of the file ExecutionDelegate.sol revokedApproval[from] is being compared to a boolean constant. That is unnecessary in these cases because you can just use the exclamation mark before the checked value to see if it's false

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77
line 77: 
```
- require(revokedApproval[from] == false, "User has revoked approval");
+ require(!revokedApproval[from], "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92
line 92:
```
- require(revokedApproval[from] == false, "User has revoked approval");
+ require(!revokedApproval[from], "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108
line 108:
```
- require(revokedApproval[from] == false, "User has revoked approval");
+ require(!revokedApproval[from], "User has revoked approval");
```

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124
line 124:
```
- require(revokedApproval[from] == false, "User has revoked approval");
+ require(!revokedApproval[from], "User has revoked approval");
```

