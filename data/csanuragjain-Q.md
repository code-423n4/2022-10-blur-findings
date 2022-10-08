## _verifyProof allows empty proofs

Contract:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L33

Issue:
_verifyProof allows empty proofs and in that case it expects the leaf to equal the root, because no hashing and iteration is taking place.

Recommendation:
Disallow empty proofs.

___

## Settle order will fail even on correct time

Contract:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L283

Issue:
In _canSettleOrder function, Order should allowed to be settled even when block.timestamp = expirationTime or listingTime = block.timestamp , but seems like contract has missed these conditions

Recommendation:
Revise the conditions like below:

```
return (listingTime <= block.timestamp) && (expirationTime == 0 || block.timestamp <= expirationTime);
```

___

## Usage of unsafe transferFrom

Contract:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L125

Issue:
In transferERC20 function, It seems the transfer of ERC20 token is made making use of transferFrom function instead of safeTransferFrom. So, even if transfer fails contract will have no way to know the same and receiver will lose the funds

Recommendation:
Kindly use safeTransferFrom function instead of transferFrom 

___

## Cancel order can be frontRun

Contract:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L181

Issue:
If Seller wants to cancel an Order A due to potential loss in trade, Buyer who is watching mempool can simply frontrun this call by calling the execute function with higher gas fees. Even though Seller did not wanted to execute this transaction, buyer will still make it happen

Recommendation:
Use flashbots which will hide cancelOrder from mempool

___

## Higher value of nonce cannot be cancelled

Contract:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L181

Issue:
1. It is only possible to cancel the current nonce. The other way is to increment nonce one by one in order to pass required nonce
2. The problem here is, if user has signed an order with very large nonce value say 10000000 then he will need to iterate upto this value 1 by 1 in order to cancel this which is impractical

Recommendation:
Allow user to jump to a required nonce value directly

___

## price>0 check is missing

Contract:
 https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L145

Issue:
It is not checked whether the current order has price set as 0. If 0 price is set then buyer is paying nothing and obtaining the nft from seller which is incorrect

Recommendation:
Add a new check

```
require(price>0, "Incorrect price");
```

___

## Recover is not checked properly

Contract:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L365

Issue:
In _validateUserAuthorization function, It is not checked whether _recover returns 0 address. If trader is also 0 address then user authorization is success and user can create an order on behalf of 0 address

Recommendation:
Revert if _recover returns 0 address

___

## Basic validation missing

Contract: 
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134

Issue:
1. It is not checked whether seller and buyer are not same. This could result in a transaction which does nothing
2. Zero address validation is missing
3. Buyer side is never checked

Recommendation:
1. Add a check to confirm that seller!=buyer
2. For setting Oracle check that new Oracle is not address 0
3. Add a check require(sell.order.side == Side.Sell && buy.order.side == Side.Buy); in execute function