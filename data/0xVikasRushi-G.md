# ARRAY.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

## 1-  calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first
```
function cancelOrders(Order[] calldata orders) external {
        for (uint8 i = 0; i < orders.length; i++) {
            cancelOrder(orders[i]);
        }
    }
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

## Second Instance  - calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first:
```
  for (uint8 i = 0; i < fees.length; i++) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
        }
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476




## Third Instance  - Memory array takes  extra mload operation (3 additional gas for each iteration except for the first),
```
     for (uint256 i = 0; i < proof.length; i++) {
            bytes32 proofElement = proof[i];
            computedHash = _hashPair(computedHash, proofElement);
        }
```
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38