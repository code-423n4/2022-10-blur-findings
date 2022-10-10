https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L508

**If the seller is a CA, the function 'transfer()' may be revert.**

- The gas of `transfer()` is fixed at 2300. If the seller is a contract and uses more than 2300 gas in the `receive()` function, the token cannot be sold, and the seller must cancel the order using the extra cost and transfer ERC721 or ERC1155 to EOA for transaction again.

If it is intended to act like this, document the associated risks for contract accounts.

If it is not intended, change transfer function to call function.