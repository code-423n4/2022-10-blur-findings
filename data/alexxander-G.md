# Gas Optimizations Report

## Example Improvements in MerkleVerifier.sol
### [G-01] Caching variables that are not used multiple times incurs extra gas costs

Here `proof[i]` is cached, however it is used only once per iteration. 
```
function _computeRoot(
        bytes32 leaf,
        bytes32[] memory proof
    ) public pure returns (bytes32) {
        bytes32 computedHash = leaf;
        for (uint256 i = 0; i < proof.length; i++) {
            bytes32 proofElement = proof[i];
            computedHash = _hashPair(computedHash, proofElement);
        }
        return computedHash;
    }
```

### [G-02] Use `calldata` instead of `memory` when only reading passed arguments
In `_verifyProof()` and `_computeRoot()` the `proof` array is only read therefore it should be passed as `calldata` to save gas, moreover, the functions wil now also conform better with OpenZeppelin's implementation of  MerkleProof https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/MerkleProof.sol
```
function _verifyProof(
        bytes32 leaf,
        bytes32 root,
        bytes32[] memory proof
    ) public pure {
        bytes32 computedRoot = _computeRoot(leaf, proof);
        if (computedRoot != root) {
            revert InvalidProof();
        }
    }
```
### [G-03] Incrementing with  `i++` incurs more gas cost  than `++i`
```
for (uint256 i = 0; i < proof.length; i++) { 
           /*
               increment should be changed to ++i
           */
        }
```
With implementing [G-01], [G-02] and [G-03] (see code below) the accumulated gas cost in executing MerkleVerify.sol in the provided tests goes from 207707 to 184585 gas.  
```
function _verifyProof(
        bytes32 leaf,
        bytes32 root,
        bytes32[] calldata proof
    ) external pure {
        if (_computeRoot(leaf, proof) != root) {
            revert InvalidProof();
        }
    }
function _computeRoot(
        bytes32 leaf,
        bytes32[] calldata proof
    ) public pure returns (bytes32) {
        bytes32 computedHash = leaf;
        for (uint256 i = 0; i < proof.length; ++i) {
            computedHash = _hashPair(computedHash, proof[i]);
        }
        return computedHash;
    }
```
## Example Improvements in BlurExcahnge.sol
### [G-04] The solidity compiler version used (0.8.13) allows the use of Custom Errors which can be used in the place of `require()` and `revert(string)` to save gas. Below is an example of using custom errors in _transferFees() insted of require().
```
// Custom Error
error FEE_GREATER_THAN_PRICE(uint256 totalFee, uint256 price);
``` 
```
function _transferFees(
        Fee[] calldata fees,
        address paymentToken,
        address from,
        uint256 price
    ) internal returns (uint256) {
        uint256 totalFee = 0;
        for (uint8 i = 0; i < fees.length; ++i) // Already mentioned
        { 
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
        }

        if(totalFee > price) {
            revert FEE_GREATER_THAN_PRICE({
                totalFee: totalFee,
                price: price
            });
        }
        //require(totalFee <= price, "Total amount of fees are more than the price");

        /* Amount that will be received by seller. */
        uint256 receiveAmount = price - totalFee;
        return (receiveAmount);
    }
```

### [G-05] Length of arrays could be cached so that `.length` is not called on every iteration, however it's important to mention that for smaller arrays the cost of caching can be greater  than calling `.length`, therefore it depends on what is the expected length of arrays to determine to use the cache or not. Example of caching below from `_transferFees()`.  
```
uint256 array_length = fees.length;
        for (uint8 i = 0; i < array_length; ++i) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
        }
```

### [G-06] Use `unchecked {}` expressions to save gas when there is no possibility of over/under flow. 



