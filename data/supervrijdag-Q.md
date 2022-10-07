In the file BlurExchange.sol between the lines 544-560 is the word "what" used as function name. 

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