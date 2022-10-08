# L-01  Manual input of Chain id

There is a chance of inputting the wrong chain id , that can break the EIP712 signing flow.


Instead use inline assembly to get the chain Id

Recommended Steps

```solidity

assembly{
chainId: chainId()
}

```


# N-01 Uppercase for constants

Use uppercase for constant variables in solidity

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57

# N-02 Improve readibility

Use 100_00 instead of 10000 

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L59




