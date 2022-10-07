# Gas Report

- [G-01: use uint256 for reentrancy guard](#g-01-use-uint256-for-reentrancy-guard)
- [G-02: ExecutionDelegate's own approval system is a waste of gas](#g-02-executiondelegates-own-approval-system-is-a-waste-of-gas)
- [G-03: use unchecked when incrementing a loop's iterator](#g-03-use-unchecked-when-incrementing-a-loops-iterator)
- [G-04: use ++i instead of i++](#g-04-use-i-instead-of-i)
- [G-05: incrementNonce() can be rewritten to save a SLOAD](#g-05-incrementnonce-can-be-rewritten-to-save-a-sload)

## G-01: use uint256 for reentrancy guard

Storing a `bool` to storage is more expensive than an `uint256` because the bool has to be packed to 32 bytes.

For a gas optimized ReentrancyGuard contract, check out the solmate: https://github.com/transmissions11/solmate/blob/main/src/utils/ReentrancyGuard.sol

## G-02: ExecutionDelegate's own approval system is a waste of gas

To approve the contract to access your funds you have to use the ERC20 token's approval system & the ExecutionDelegate's own one. Having that additional approval layer is a waste of gas and doesn't really provide any more protection.

You'd save a SLOAD with every transfer.

## G-03: use unchecked when incrementing a loop's iterator

The value can't realistaclly overflow because of the loop's condition. Thus you can use unchecked to save the opcodes used for the overflow check.

```
./contracts/PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
./contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
./contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
./contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
./contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```

## G-04: use ++i instead of i++

`i++` saves the original value in memory and returns that while `++i` just returns the new value. By not saving the original value to memory you save a little gas.

```
./contracts/PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
./contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
./contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
./contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
./contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
```

## G-05: incrementNonce() can be rewritten to save a SLOAD

Instead of
```sol
function incrementNonce() external {
    nonces[msg.sender] += 1;
    emit NonceIncremented(msg.sender, nonces[msg.sender]);
}
```

you can do:
```sol
function incrementNonce() external {
    emit NonceIncremented(msg.sender, ++nonces[msg.sender]);
}
```

Same principle as explained in the above issue.
