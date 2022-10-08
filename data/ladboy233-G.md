
   
## function can be marked as external instead of public


https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L95


```
    function initialize(
```
            

## ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT�S USED IN FOR-LOOPS (--I/I-- TOO)
    
Saves 6 gas per loop


https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199


```
        for (uint8 i = 0; i < orders.length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476


```
        for (uint8 i = 0; i < fees.length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77


```
        for (uint256 i = 0; i < length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77


```
        for (uint256 i = 0; i < fees.length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38


```
        for (uint256 i = 0; i < proof.length; i++) {
```
            

## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset


https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199


```
        for (uint8 i = 0; i < orders.length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476


```
        for (uint8 i = 0; i < fees.length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77


```
        for (uint256 i = 0; i < fees.length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38


```
        for (uint256 i = 0; i < proof.length; i++) {
```
            

## IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED


https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199


```
        for (uint8 i = 0; i < orders.length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476


```
        for (uint8 i = 0; i < fees.length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77


```
        for (uint256 i = 0; i < length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77


```
        for (uint256 i = 0; i < fees.length; i++) {
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38


```
        for (uint256 i = 0; i < proof.length; i++) {
```
            

## ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
   

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L45


```
            abi.encode(
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L61


```
            abi.encode(
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L91


```
                abi.encode(
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L107


```
                abi.encode(nonce)
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L132


```
            keccak256(abi.encode(
```
            

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L147


```
            keccak256(abi.encode(
```
            