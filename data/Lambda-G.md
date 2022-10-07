- Instead of using a boolean for the reentrancy lock, an integer would be more gas-efficient. This is described in detail in OZ's reentrancy guard (https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol):
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.

    // The values being non-zero value makes deployment a bit more expensive,
    // but in exchange the refund on every call to nonReentrant will be lower in
    // amount. Since refunds are capped to a percentage of the total
    // transaction's gas, it is best to keep them low in cases like this one, to
    // increase the likelihood of the full refund coming into effect.
```
- In multiple for loops, the loop iteration can be marked as `unchecked` because an overflow is not possible (as the iterator is bounded):
```
contracts/PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```