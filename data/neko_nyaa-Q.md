# Low-risk findings

## [L-01] There is no cap on the transfer fee

There is no check on the cap on transfer fee, the only theoretical cap is the trading price itself.

**Proof of concept**: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L469

**Impact**: Even though the user has signed the fee as part of the trading order, frequent traders for example, may not check all of their signed messages for any changes in fee, thus there is potential for loss of value due to fee.

**Recommended mitigation method**: Add a cap to the total fee (e.g. 10% of the trading price).

## [L-02] Seller bearing all fees is an inconvenient and unconventional way to do it, furthermore this is not properly documented

**Proof of concept**: https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L151

In any trading matchings, only the seller's fee is accounted for. This is an issue for the following reasons:
- This is an inconvenient and unconventional method. Usually it is the order maker who has to account for the fees, and the maker can be either the buyer or the seller.
- There is no check in `StandardPolicyERC1155` or `StandardPolicyERC721` that the fee structure of two orders are the same, or any of these given sides' fee are empty. However since any order requires the signature of the trader, this implies both traders consent to their given fee structures, but then it turns out that the fees are not distributed as to how the traders acknowledged.
    - Even if the buyer knows they have zero fees incurred, then the fact that they have to sign a possibly non-empty fee structure is itself a Low/QA issue.
- Nowhere in the code is this behavior documented. The only way to find out is to read this specific part of the code, or other related parts of the codebase to reach this specific part as well.

**Remarks**: It is hard to assess the risk of this finding, as the intention was not documented anywhere in the repository, which makes it anywhere from a QA/low issue (documentation, fee sanity checks) to a high risk issue (mishandling of fees).

**Recommended mitigation method**: Properly document the fee mechanism for the maker/taker (or the buyer/seller), as well as consider adding sanity checks for the fee structures of each order.

# Non-risk/QA findings

## [N-01] `require` statements should come with reason string if possible

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183

## [N-02] Function is unused anywhere

The function `_verifyProof` in the `MerkleVerifier` is unused anywhere in the code. If this is intended, I believe it should be documented so in the code

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17

## [N-03] Non-assembly method available

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L555

Can be changed to `uint size = address(what).code.length;`