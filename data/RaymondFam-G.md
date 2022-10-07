## Use Custom Errors Instead of Require to Save Gas
Solidity 0.8.4 and above features custom errors which are cheaper both in deployment and runtime cost than revert strings. Custom errors save about 50 gas each time they’re hit by not needing to allocate and store the revert string. Please visit the following link for additional details:

https://blog.soliditylang.org/2021/04/21/custom-errors/

Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139-L143
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77

## Shorten Require Messages to Less Than 32 characters
Strings that are more than 32 characters will require more than 1 storage slot, costing more gas. Consider reducing the message length to less than 32 characters on all require() statements not intended to be replaced by custom errors. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482

## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
  solidity: {
    version: "0.8.13",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI

## Private/Internal Function Embedded Modifier Reduces Contract Size
Consider having the logic of a modifier embedded through an internal or private (if no contracts inheriting) function to reduce contract size if need be. For instance, the following instance of modifier may be rewritten as follows:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L35-L38

```
    function _whenOpen() private view {
        require(isOpen == 1, "Closed");
    }

    modifier whenOpen() {
        _whenOpen();
        _;
    }
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =). As an example, consider replacing <= with the strict counterpart < in the following line of code:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482

```
        require(totalFee < price + 1, "Total amount of fees are more than the price");
```
## += and -= Costs More Gas
`+=` generally costs 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop. As an example, the following line of code could be rewritten as:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L479

```
            totalFee = totalFee + fee;
```
## ++i costs less gas compared to i++
++i costs less gas compared to i++ or i += 1 for unsigned integers considering the pre-increment operation is cheaper (about 5 GAS per iteration).

i++ increments i and makes the compiler create a temporary variable for returning the initial value of i. In contrast, ++i returns the actual incremented value without making the compiler do extra job.

Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

## Unchecked SafeMath Saves Gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas in the for loop below as an example:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77-L79

```
        for (uint256 i = 0; i < length;) {
            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);

            unchecked {
              ++i;
          }
        }
```
## No Need to Initialize Variables with Default Values
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). If you explicitly initialize it with its default value, you will be incurring more gas. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L475-L476
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10

## > 0 Is More Expensive Than != 0 for Unsigned Integers
!= 0 costs 6 less GAS compared to > 0 for unsigned integers in conditional statements with the optimizer enabled. Here is one of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L557

## Inline Codes for Internal Function
The following internal view function, `_exists()`, that is only called by `_executeTokenTransfer()` could be embedded inline to save gas:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L548-L558

## Using Booleans Costs More Storage Overhead
According to Openzeppelin's `ReentrancyGuard.sol`:

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.

Consider using uint256(1) and uint256(2) for true/false to avoid repeated:

1. Gwarmaccess that would cost 100 gas for a warm account or storage access, and
2. Gsset that would cost 20,000 gas for an SSTORE operation switching the storage value to non-zero from zero, i.e, ‘false’ to ‘true’.

Here is one of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10

## calldata and memory
When running a function we could pass the function parameters as calldata or memory for variables such as strings, bytes, structs, arrays etc. If we are not modifying the passed parameter, we should pass it as calldata because calldata is more gas efficient than memory.

Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. However, it alleviates the compiler from the `abi.decode()` step that copies each index of the calldata to the memory index, bypassing the cost of 60 gas on each iteration. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L20
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L35

## External costs less gas than public visibility
Functions not internally called may have its visibility changed to external to save gas. Here is one of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L102

## Payable Access Control Functions Costs Less Gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`. Here are some of the instances entailed:

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L36
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L244
