[Cross-chain replay attacks are possible for matching or cancelling an order]

Order made in one EVM chain can be re-applied to another EVM chain
Sell order, buy order and cancel order hashes (See BlurExchange.sol#L136-L137, BlurExchange.sol#L185) do not have chain.id in the signed data.

As chainId is a parameter in the initialize function (See BlurExchange.sol#L96), it is good to add chain id while signing (See EIP712.sol#L84-L110)

See

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L136-L137
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L185
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L84-L110
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L96