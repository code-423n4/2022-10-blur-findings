
G001 - Don't Initialize Variables with Default Value
---

Instances Include: 

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10


Mitigation:

Explicitly initializing a variable with it's default value costs unnecesary gas.

Do this: 

```
  bool reentrancyLock;
```

Instead of this: 

```
  bool reentrancyLock = false;
```

G002 - Cache Array Length Outside of Loop
---


Instances Include:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38


Mitigation: 

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Do this

```
uint feeLength = fees.length

 for (uint8 i = 0; i < feeLength; i++) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
        }


```


Instead of this:

```

 for (uint8 i = 0; i < fees.length; i++) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
        }

```


G009 - Make Function external instead of public
---


Instances Include: 

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L21

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L36


Mitigation:

Do this 


```

 function _computeRoot(
        bytes32 leaf,
        bytes32[] memory proof
    ) external pure returns (bytes32) {
        bytes32 computedHash = leaf;
        for (uint256 i = 0; i < proof.length; i++) {
            bytes32 proofElement = proof[i];
            computedHash = _hashPair(computedHash, proofElement);
        }
        return computedHash;
    }


```


Instead of this:

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

G011 - Unnecessary checked arithmetic in for loop
---


Instances Include:


https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L198

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

Mitigation:

Do this:


```
uint orderLength = orders.length;

    function cancelOrders(Order[] calldata orders) external {
        for (uint8 i = 0; i < orders.length; ) {
            cancelOrder(orders[i]);



          unchecked {i++;}

        }
    }

```

Instead of this:


```

    function cancelOrders(Order[] calldata orders) external {
        for (uint8 i = 0; i < orders.length; i++) {
            cancelOrder(orders[i]);
        }
    }


```


G012 - Use Prefix Increment instead of Postfix Increment if possible
---


Instances Include:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77



Mitigation:


The difference between the prefix increment and postfix increment expression lies in the return value of the expression.

The prefix increment expression (++i) returns the updated value after it's incremented. The postfix increment expression (i++) returns the original value.

The prefix increment expression is cheaper in terms of gas.

Consider using the prefix increment expression whenever the return value is not needed.

Note to be careful using this optimization whenever the expression's return value is used afterwards, e.g. uint a = i++ and uint a = ++i result in different values for a.


Do this:


```
        for (uint256 i = 0; i < length; ++i) {
            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
        }



```

Instead of this


```
        for (uint256 i = 0; i < length; i++) {
            whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
        }

```


Background Info : 

https://m1guelpf.blog/d0gBiaUn48Odg8G2rhs3xLIjaL8MfrWReFkjg8TmDoM