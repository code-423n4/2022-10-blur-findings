## 1. Use `require(x)` instead of `require(x == true)`

_contracts/BlurExchange.sol:_ [L267](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L267)

_contracts/ExecutionDelegate.sol:_ [L92](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92)
[L108](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108)
[L124](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L124)

## 2. Strings messages in `require` statements should not be longer than 32 bytes of length

_contracts/BlurExchange.sol:_ [L482](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482)

_contracts/ExecutionDelegate.sol:_ [L22](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22)

## 3. Place `i++` in an `unchecked` blocks in for-loops

_contracts/lib/MerkleVerifier.sol:_ [L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

_contracts/lib/EIP712.sol:_ [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)

_contracts/BlurExchange.sol:_ [L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
[L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)

_contracts/PolicyManager.sol:_ [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)

## 4. Consider marking functions as `payable` if there is no risk of sending value through them

### This change will save gas each time a function is called

_contracts/ExecutionDelegate.sol:_ [L36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36)
[L45](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L45)

_contracts/PolicyManager.sol:_ [L25](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L25)
[L36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L36)

_contracts/BlurExchange.sol:_ [L43](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L43)
[L47](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L47)
[L53](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53)

## 5. Calldata is cheaper than memory for function input

_contracts/lib/EIP712.sol:_ [L39](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L39)

_contracts/lib/MerkleVerifier.sol:_ [L20](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L20)
[L35](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L35)

## 6. Function that are called only once can be inlined in the calling function

### This change will save around 30 gas units

_contracts/lib/MerkleVerifier.sol:_ [L45](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L45)
[L49](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L49)

## 7. Cache storage variables in function call stack to save gas

_contracts/PolicyManager.sol:_ [L71-L78](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L71-L78)

## 8. Prefix incrementing and decrementing costs around 6 gas less than the postfix ones

### e.g. ++var is cheaper than var++

_contracts/lib/MerkleVerifier.sol:_ [L38](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38)

_contracts/BlurExchange.sol:_ [L199](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199)
[L476](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L476)

_contracts/PolicyManager.sol:_ [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77)

_contracts/lib/EIP712.sol:_ [L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L77)

## 9. Use 1 and 2 for true and false

_contracts/lib/ReentrancyGuarded.sol:_ [L10](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10)

## 10. `public` storage `constant`(and `immutable`) variable should be `private`

### saves tons of gas on deployment

_contracts/lib/EIP712.sol:_ [L23](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L23)
[L26](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L26)
[L29](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L29)

_contracts/BlurExchange.sol:_ [L57](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57)
[L58](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L58)
[L59](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59)

## 11. Custom error are cheaper than string messages

_contracts/BlurExchange.sol:_ [L36](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L36)
[L139](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L139)
[L142](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L142)
[L143](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L143)
[L228](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L228)
[L318](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L318)
[L407](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L407)
[L424](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L424)
[L431](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L431)
[L482](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L482)
[L534](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L534)

_contracts/PolicyManager.sol:_ [L26](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L26)
[L37](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L37)

_contracts/ExecutionDelegate.sol:_ [L22](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L22)
[L77](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L77)
[L92](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L92)
[L108](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L108)

_contracts/lib/ReentrancyGuarded.sol:_ [L14](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L14)

## 12. Use `constant` and `immutable` for constants

_contracts/PolicyManager.sol:_ [L16](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L16)

_contracts/lib/EIP712.sol:_ [L37](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L37)
