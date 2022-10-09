
## Gas

### There is un-used variable

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388

bytes32[] memory merklePath not used.
Replace to `_`.

### Remove duplicate logic

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37

At `openzeppelin/contracts/utils/structs/EnumerableSet.sol#add and #remove` has logic that check element is contain or not.
So remove contain logic can save gas.