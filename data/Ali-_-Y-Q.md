# Function returns empty array
## Proof of Concept
In PolicyManger contract Function `viewWhitelistedPolicies` will return empty array if size
is zero and cursor equals to ` _whitelistedPolicies.length`.
```solidity
 function viewWhitelistedPolicies(uint256 cursor, uint256 size)
        external
        view
        override
        returns (address[] memory, uint256)
    {
        uint256 length = size;

        if (length > _whitelistedPolicies.length() - cursor) {
            length = _whitelistedPolicies.length() - cursor;
        }

        address[] memory whitelistedPolicies = new address[](length);

        for (uint256 i = 0; i < length; i++) {
            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
        }

        return (whitelistedPolicies, cursor + length);
    }
```
#### In the return:
- whitelistedPolicies will be empty
- cursor + length will be cursor

## Impact 

The impact depends on the usage of the function.
## Recommended Mitigation Steps

Add condition for size equals to zero
`require(size != 0,"Size is zero');`

