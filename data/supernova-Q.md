# L-01  Manual input of Chain id

There is a chance of inputting the wrong chain id , that can break the EIP712 signing flow.


Instead use inline assembly to get the chain Id

Recommended Steps

```solidity

assembly{
chainId: chainId()
}

```
